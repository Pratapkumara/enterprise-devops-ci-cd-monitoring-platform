pipeline {

    agent any

    tools {
        maven 'maven3'
        jdk 'JDK21'
    }

    environment {

        IMAGE_NAME = "pratapkumar1/devops-app"
        IMAGE_TAG = "1.2"

        CONTAINER_NAME = "devops-app-container"

        PORT = "8081"

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

                        echo "Checking Jar"

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
                            -Dsonar.java.binaries=target/classes \
                            -Dsonar.working.directory=.scannerwork

                            echo "Checking Sonar report"

                            find . -name report-task.txt || true

                            cat .scannerwork/report-task.txt || true
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

                        docker images ${IMAGE_NAME}
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
                        echo "Docker Login"

                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" \
                        --password-stdin

                        echo "Pushing Docker Image"

                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
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

                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${PORT}:8080 \
                    ${IMAGE_NAME}:${IMAGE_TAG}

                    docker ps
                '''
            }
        }
    }

    post {

        success {
            echo "🚀 CI/CD Pipeline Completed Successfully"
        }

        failure {
            echo "❌ Pipeline Failed"
        }

        always {
            cleanWs()
        }
    }
}
