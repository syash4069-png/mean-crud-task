pipeline {
    agent any

    environment {
        // Docker Hub repos
        DOCKERHUB_BACKEND  = "syashsingh/mean-backend"
        DOCKERHUB_FRONTEND = "syashsingh/mean-frontend"

        // App Server details
        SSH_HOST = "13.204.225.105"
        SSH_USER = "ubuntu"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
                echo "Repository cloned. Listing workspace..."
                sh "ls -R"
            }
        }

        stage('Build Backend Image') {
            steps {
                echo "Building backend Docker image..."
                sh """
                    docker build -t ${DOCKERHUB_BACKEND}:latest backend
                """
            }
        }

        stage('Build Frontend Image') {
            steps {
                echo "Building frontend Docker image..."
                sh """
                    docker build -t ${DOCKERHUB_FRONTEND}:latest frontend
                """
            }
        }

        stage('Push Images to Docker Hub') {
            environment {
                DOCKERHUB = credentials('meanApp')  // Username + PAT
            }
            steps {
                echo "Logging into Docker Hub..."
                sh """
                    echo "\$DOCKERHUB_PSW" | docker login -u "\$DOCKERHUB_USR" --password-stdin
                """

                echo "Pushing backend image..."
                sh "docker push ${DOCKERHUB_BACKEND}:latest"

                echo "Pushing frontend image..."
                sh "docker push ${DOCKERHUB_FRONTEND}:latest"
            }
        }

        stage('Deploy to EC2 Server') {
            steps {
                echo "Starting deployment on EC2..."
                sshagent(credentials: ['app-ec2-ssh']) {

                    echo "Copying docker-compose.yml to EC2 server..."
                    sh """
                        scp -o StrictHostKeyChecking=no docker-compose.yml \
                        ${SSH_USER}@${SSH_HOST}:/opt/mean-app/docker-compose.yml
                    """

                    echo "Restarting containers on EC2..."
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@13.204.225.105 \
                         "cd /opt/mean-app && \
                          sudo docker-compose down || true && \
                          sudo docker-compose pull && \
                          sudo docker-compose up -d"

                    """
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Deployment Successful! Images are updated and containers restarted."
        }
        failure {
            echo "❌ Deployment Failed. Check Jenkins logs and fix errors."
        }
    }
}
