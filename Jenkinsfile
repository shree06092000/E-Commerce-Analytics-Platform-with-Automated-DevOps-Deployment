pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'bhagyashreejoshi06'
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/analytics-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/analytics-frontend"
        GITHUB_REPO    = 'https://github.com/shree06092000/E-Commerce-Analytics-Platform-with-Automated-DevOps-Deployment.git'
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Pulling latest code from GitHub...'
                git branch: 'main', url: "${GITHUB_REPO}"
            }
        }

        stage('Build Backend Image') {
            steps {
                echo 'Building backend Docker image...'
                sh 'docker build -t ${BACKEND_IMAGE}:latest ./backend'
                sh 'docker tag ${BACKEND_IMAGE}:latest ${BACKEND_IMAGE}:${BUILD_NUMBER}'
            }
        }

        stage('Build Frontend Image') {
            steps {
                echo 'Building frontend Docker image...'
                sh 'docker build -t ${FRONTEND_IMAGE}:latest ./frontend'
                sh 'docker tag ${FRONTEND_IMAGE}:latest ${FRONTEND_IMAGE}:${BUILD_NUMBER}'
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo 'Pushing images to DockerHub...'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ${BACKEND_IMAGE}:latest'
                    sh 'docker push ${BACKEND_IMAGE}:${BUILD_NUMBER}'
                    sh 'docker push ${FRONTEND_IMAGE}:latest'
                    sh 'docker push ${FRONTEND_IMAGE}:${BUILD_NUMBER}'
                }
            }
        }

        stage('Cleanup') {
            steps {
                echo 'Removing local images to free space...'
                sh 'docker rmi ${BACKEND_IMAGE}:latest || true'
                sh 'docker rmi ${FRONTEND_IMAGE}:latest || true'
                sh 'docker rmi ${BACKEND_IMAGE}:${BUILD_NUMBER} || true'
                sh 'docker rmi ${FRONTEND_IMAGE}:${BUILD_NUMBER} || true'
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded! Images pushed to DockerHub.'
        }
        failure {
            echo 'Pipeline failed! Check the logs above.'
        }
    }
}
