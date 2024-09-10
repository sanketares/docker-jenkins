pipeline {
    agent {
    docker {
        image 'amazonlinux'
    }
}
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials' // ID for your Docker Hub credentials
        DOCKER_IMAGE_NAME = 'sanket406/newimage'
        DOCKER_TAG = 'latest'
    }
    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_TAG}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        // Push the Docker image
                        docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_TAG}").push("${DOCKER_TAG}")
                    }
                }
            }
        }
    }
}
