pipeline {

    agent any

    tools {
        maven 'maven3'
    }


    environment {

        IMAGE_NAME = "devops-app"
        IMAGE_TAG  = "1.2"

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
                    mvn clean package -DskipTests

                    echo "Checking Build Artifact..."
                    ls -lh target/app-1.0.0.jar
                    '''
                }
            }
        }



        stage('SonarQube Analysis') {

            steps {

                dir('app') {

                    withSonarQubeEnv('sonar-server') {

                        sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=devops-app \
                        -Dsonar.projectName=devops-app \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes
                        """
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

                    sh """

                    docker build --no-cache \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} .

                    docker images | grep ${IMAGE_NAME}

                    """
                }
            }
        }





        stage('Trivy Security Scan') {

            steps {

                sh '''

                trivy image \
                --skip-version-check \
                --no-progress \
                --exit-code 1 \
                --severity CRITICAL \
                ${IMAGE_NAME}:${IMAGE_TAG}

                '''
            }
        }





        stage('Deploy Container') {

            steps {

                sh '''

                echo "Stopping old container..."

                docker rm -f springboot-app || true


                echo "Starting new container..."

                docker run -d \
                --name springboot-app \
                --restart always \
                -p 8081:8080 \
                ${IMAGE_NAME}:${IMAGE_TAG}



                echo "Checking container..."

                sleep 30

                docker ps | grep springboot-app


                echo "Application Health Check..."

                curl -f http://localhost:8081 || exit 1


                '''
            }
        }


    }



    post {


        success {

            echo '✅ CI/CD Pipeline Completed Successfully 🚀'

        }


        failure {

            echo '❌ CI/CD Pipeline Failed'

        }


        always {

            cleanWs()

        }

    }

}
