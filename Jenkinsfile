pipeline {

    agent any

    tools {
        jdk 'jdk21'
        maven 'maven3'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {

        IMAGE_NAME = "pratapkumar1/devops-app"
        IMAGE_TAG = "${BUILD_NUMBER}"

        CONTAINER_NAME = "devops-app-container"

        PORT = "8082"

        SONAR_PROJECT_KEY = "devops-app"
        SONAR_PROJECT_NAME = "devops-app"

        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build') {
            steps {

                dir('app') {

                    sh '''
                        echo "Cleaning project..."
                        mvn clean

                        echo "Building application..."
                        mvn package -DskipTests

                        echo "Generated JAR"
                        ls -lh target/*.jar
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {

            steps {

                dir('app') {

                    withSonarQubeEnv('sonarqube') {

                        sh '''
                            ${SCANNER_HOME}/bin/sonar-scanner \
                            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                            -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                            -Dsonar.sources=src \
                            -Dsonar.java.binaries=target/classes
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
                        docker build \
                        -t ${IMAGE_NAME}:${IMAGE_TAG} \
                        -t ${IMAGE_NAME}:latest .
                    '''
                }
            }
        }

        stage('Trivy Scan') {

            steps {

                sh '''
                    trivy image \
                    --severity HIGH,CRITICAL \
                    --no-progress \
                    --exit-code 0 \
                    ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Docker Push') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASS" | docker login \
                        -u "$DOCKER_USER" \
                        --password-stdin

                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker push ${IMAGE_NAME}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy') {

            steps {

                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true

                    docker pull ${IMAGE_NAME}:${IMAGE_TAG}

                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    --restart unless-stopped \
                    -p ${PORT}:8080 \
                    ${IMAGE_NAME}:${IMAGE_TAG}

                    sleep 20

                    docker ps

                    curl -I http://localhost:${PORT} || true
                '''
            }
        }

    }

    post {

        success {

            echo "=========================================="
            echo "Pipeline Completed Successfully"
            echo "Application Running On:"
            echo "http://15.207.71.93:8082"
            echo "=========================================="
        }

        failure {

            echo "=========================================="
            echo "Pipeline Failed"
            echo "Check Console Output"
            echo "=========================================="
        }

        always {

            cleanWs()
        }
    }
}
