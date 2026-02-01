pipeline {

    agent any

    environment {
        IMAGE_NAME = "ramesh0621/spicy-kitchen"
        IMAGE_TAG = "v1"
        FULL_IMAGE_NAME = "${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {

        stage('Check out code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {

            steps {
                echo "Building Docker Image"
                sh '''
                    docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('push to docker hub') {

            steps {
                echo "Pushing Docker Image to Docker Hub"
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    sh '''
                        echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage ('Clean Up') {
            steps {
                echo "Cleaning up local Docker images"
                sh '''
                    docker rmi $IMAGE_NAME:$IMAGE_TAG || true
                '''
            }
        }

        stage('Deploy to container using Docker-Compose') {
            steps {
                echo "Deploying Docker Image to Container using docker compose"
                sh '''

                    docker-compose down || true
                    docker-compose up -d
                '''
            }
        }
    }
}
