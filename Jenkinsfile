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

        stage('Build Docker') {
            steps {
                sh "docker build -t ${APP_NAME}:${env.BRANCH_NAME} ."
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker rm -f ${APP_NAME}-${env.BRANCH_NAME} || true
                docker run -d -p 3001:3000 \
                -e BRANCH=${env.BRANCH_NAME} \
                --name ${APP_NAME}-${env.BRANCH_NAME} \
                ${APP_NAME}:${env.BRANCH_NAME}
                """
            }
        }

        stage('Deploy Info') {
            steps {
                echo "Deployed ${env.BRANCH_NAME}"
            }
        }
    }
}