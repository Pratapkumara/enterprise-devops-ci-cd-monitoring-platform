```groovy
pipeline {

    agent any

    tools {
        maven 'maven3'
        jdk 'jdk21'
    }

    environment {

        IMAGE_NAME = "devops-app"
        IMAGE_TAG  = "1.0"

        SCANNER_HOME = tool 'sonar-scanner'
    }


    stages {


        stage('Checkout Code') {

            steps {

                checkout scm

            }
        }



        stage('Build Maven') {

            steps {

                dir('app') {

                    sh '''
                    echo "Building Spring Boot Application"

                    mvn clean package -DskipTests

                    echo "Checking JAR file"

                    ls -lh target/*.jar
                    '''

                }

            }

        }



        stage('SonarQube Analysis') {

            steps {

                dir('app') {

                    withSonarQubeEnv('SonarQube') {

                        sh '''

                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=devops-app \
                        -Dsonar.projectName=devops-app \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes

                        '''

                    }

                }

            }

        }




        stage('Quality Gate') {

            steps {

                timeout(time: 5, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: true

                }

            }

        }





        stage('Build Docker Image') {

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





        stage('Trivy Image Scan') {

            steps {

                sh '''

                echo "Scanning Docker Image"

                trivy image \
                --no-progress \
                --severity HIGH,CRITICAL \
                --exit-code 1 \
                ${IMAGE_NAME}:${IMAGE_TAG}


                '''

            }

        }





        stage('Deploy Application') {

            steps {

                sh '''

                echo "Removing old container"

                docker rm -f springboot-app || true



                echo "Starting new container"


                docker run -d \
                --name springboot-app \
                --restart unless-stopped \
                -p 8081:8080 \
                ${IMAGE_NAME}:${IMAGE_TAG}



                echo "Waiting for application startup"

                sleep 20



                echo "Container Status"

                docker ps | grep springboot-app



                echo "Application Logs"

                docker logs --tail 50 springboot-app



                '''

            }

        }



    }



    post {


        success {

            echo "================================="
            echo "CI/CD Pipeline Successful 🚀"
            echo "================================="

        }



        failure {

            echo "================================="
            echo "CI/CD Pipeline Failed ❌"
            echo "================================="

        }



        always {

            cleanWs()

        }

    }

}
```
