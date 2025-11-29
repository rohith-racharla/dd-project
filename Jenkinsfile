pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME = 'rohithracharla'
        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/mean-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/mean-frontend"
        EC2_HOST = '52.53.192.211'
        EC2_USER = 'ubuntu'
        PROJECT_DIR = '/home/ubuntu/dd-project'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }
        
        stage('Build Backend Image') {
            steps {
                echo 'Building Backend Docker image...'
                dir('backend') {
                    script {
                        sh "docker build -t ${BACKEND_IMAGE}:${BUILD_NUMBER} ."
                        sh "docker tag ${BACKEND_IMAGE}:${BUILD_NUMBER} ${BACKEND_IMAGE}:latest"
                    }
                }
            }
        }
        
        stage('Build Frontend Image') {
            steps {
                echo 'Building Frontend Docker image...'
                dir('frontend') {
                    script {
                        sh "docker build -t ${FRONTEND_IMAGE}:${BUILD_NUMBER} ."
                        sh "docker tag ${FRONTEND_IMAGE}:${BUILD_NUMBER} ${FRONTEND_IMAGE}:latest"
                    }
                }
            }
        }
        
        stage('Push Images to Docker Hub') {
            steps {
                echo 'Logging in to Docker Hub...'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                
                echo 'Pushing Backend image...'
                sh "docker push ${BACKEND_IMAGE}:${BUILD_NUMBER}"
                sh "docker push ${BACKEND_IMAGE}:latest"
                
                echo 'Pushing Frontend image...'
                sh "docker push ${FRONTEND_IMAGE}:${BUILD_NUMBER}"
                sh "docker push ${FRONTEND_IMAGE}:latest"
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                echo 'Deploying to EC2...'
                sshagent(['ec2-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                            cd ${PROJECT_DIR} && \
                            docker compose pull && \
                            docker compose up -d && \
                            docker system prune -f
                        '
                    """
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                echo 'Verifying deployment...'
                sshagent(['ec2-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                            cd ${PROJECT_DIR} && \
                            docker compose ps && \
                        '
                    """
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            sh 'docker logout'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
