pipeline {
    agent any

    environment {
        // Docker Hub repos
        DOCKERHUB_BACKEND  = "syashsingh/mean-backend"
        DOCKERHUB_FRONTEND = "syashsingh/mean-frontend"

        // App EC2 (Docker + Nginx server)
        SSH_HOST = "13.204.225.105"
        SSH_USER = "ubuntu"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                sh """
                  docker build -t ${DOCKERHUB_BACKEND}:latest ./mean-crud-task/backend
                """
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh """
                  docker build -t ${DOCKERHUB_FRONTEND}:latest ./mean-crud-task/frontend
                """
            }
        }

        stage('Push Images to Docker Hub') {
            environment {
                DOCKERHUB = credentials('meanApp')
            }
            steps {
                sh """
                  echo "\$DOCKERHUB_PSW" | docker login -u "\$DOCKERHUB_USR" --password-stdin

                  docker push ${DOCKERHUB_BACKEND}:latest
                  docker push ${DOCKERHUB_FRONTEND}:latest
                """
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent(credentials: ['app-ec2-ssh']) {
                    sh """
                      ssh -o StrictHostKeyChecking=no ${SSH_USER}@${SSH_HOST} '
                        cd /opt/mean-app/mean-crud-task &&
                        git pull &&
                        sudo docker-compose pull &&
                        sudo docker-compose up -d
                      '
                    """
                }
            }
        }
    }
}
