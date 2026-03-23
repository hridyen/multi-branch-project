pipeline {
    agent { label 'ubuntu-agent' }

    environment {
        APP_NAME = "multi-branch-project"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${APP_NAME}:${env.BRANCH_NAME} ."
            }
        }

        stage('Run Container') {
            steps {
                script {

                    // Dynamic port allocation
                    def port = ""

                    if (env.BRANCH_NAME == "main") {
                        port = "3000"
                    } else if (env.BRANCH_NAME == "dev") {
                        port = "3001"
                    } else {
                        port = "3002"
                    }

                    echo " Running ${env.BRANCH_NAME} on port ${port}"

                    sh """
                    docker rm -f ${APP_NAME}-${env.BRANCH_NAME} || true

                    docker run -d \
                    -p ${port}:3000 \
                    -e BRANCH=${env.BRANCH_NAME} \
                    --name ${APP_NAME}-${env.BRANCH_NAME} \
                    ${APP_NAME}:${env.BRANCH_NAME}
                    """
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    echo "Checking if container is running..."
                    sh "docker ps | grep ${APP_NAME}-${env.BRANCH_NAME}"
                }
            }
        }

        stage('Deploy Info') {
            steps {
                echo " ${env.BRANCH_NAME} deployed successfully"
            }
        }
    }

    post {
        success {
            echo " Pipeline SUCCESS for ${env.BRANCH_NAME}"
        }
        failure {
            echo "Pipeline FAILED for ${env.BRANCH_NAME}"
        }
    }
}