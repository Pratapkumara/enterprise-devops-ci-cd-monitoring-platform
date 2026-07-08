pipeline {

    agent any

    tools {
        maven 'maven3'
        jdk 'JDK21'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {

        IMAGE_NAME = "pratapkumar1/devops-app"
        IMAGE_TAG = "1.2"

        CONTAINER_NAME = "devops-app-container"

        // 8081 Nginx use kar raha hai
        PORT = "8082"

        SONAR_PROJECT_KEY = "devops-app"
        SONAR_PROJECT_NAME = "devops-app"

        SCANNER_HOME = tool 'sonar-scanner'
        SONAR_HOST_URL = "http://sonarqube:9000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Checking out source code"
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                dir('app') {
                    sh '''
                        echo "Building Spring Boot Application"

                        mvn clean package -DskipTests

                        echo "Checking Generated JAR"

                        ls -lh target/*.jar
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('app') {
                    withSonarQubeEnv('sonar-server') {

                        sh '''
                            echo "Running SonarQube Scan"

                            ${SCANNER_HOME}/bin/sonar-scanner \
                              -Dsonar.host.url=${SONAR_HOST_URL} \
                              -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                              -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                              -Dsonar.sources=src \
                              -Dsonar.java.binaries=target/classes

                            echo "Sonar Scan Completed"
                        '''
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('app') {
                    sh '''
                        echo "Building Docker Image"

                        docker build \
                        -t ${IMAGE_NAME}:${IMAGE_TAG} .

                        docker images | grep ${IMAGE_NAME}
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" \
                        --password-stdin

                        docker push ${IMAGE_NAME}:${IMAGE_TAG}

                        docker logout
                    '''
                }
            }
        }

        stage('Trivy Security Scan') {
            steps {

                sh '''
                    echo "Running Trivy Scan"

                    trivy image \
                      --severity HIGH,CRITICAL \
                      --exit-code 0 \
                      --no-progress \
                      ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy Application') {

            steps {

                sh '''
                    echo "Deploying Application"

                    docker stop ${CONTAINER_NAME} || true

                    docker rm ${CONTAINER_NAME} || true

                    docker pull ${IMAGE_NAME}:${IMAGE_TAG}

                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      --restart unless-stopped \
                      -p ${PORT}:8080 \
                      ${IMAGE_NAME}:${IMAGE_TAG}

                    echo "Waiting for application..."

                    sleep 20

                    docker ps

                    curl -I http://localhost:${PORT} || true
                '''
            }
        }
    }

    post {

        success {
            echo "========================================="
            echo " CI/CD Pipeline Completed Successfully "
            echo " Application URL:"
            echo " http://<EC2-PUBLIC-IP>:8082"
            echo "========================================="
        }

        failure {
            echo "========================================="
            echo " Pipeline Failed"
            echo "Check Console Output"
            echo "========================================="
        }

        always {
            cleanWs()
        }
    }
}
