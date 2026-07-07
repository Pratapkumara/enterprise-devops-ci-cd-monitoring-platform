pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK21'
    }

    environment {

        APP_NAME = "devops-app"
        IMAGE_NAME = "devops-app"
        CONTAINER_NAME = "devops-app-container"
        PORT = "8081"

        SONAR_PROJECT_KEY = "devops-app"
        SONAR_PROJECT_NAME = "devops-app"
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


                        sonar-scanner \
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

                    echo "Building Docker Image"


                    docker build \
                    -t ${IMAGE_NAME}:latest .


                    docker images ${IMAGE_NAME}


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
                ${IMAGE_NAME}:latest


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
                ${IMAGE_NAME}:latest



                echo "Application Deployed"


                docker ps


                '''
            }
        }


    }



    post {

        success {

            echo "Pipeline Completed Successfully 🚀"

        }


        failure {

            echo "Pipeline Failed ❌"

        }


        always {

            cleanWs()

        }

    }

}
