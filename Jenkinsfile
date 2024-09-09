pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials' // ID for your Docker Hub credentials
        DOCKER_IMAGE_NAME = 'sanket406/newimage'
        DOCKER_TAG = 'latest'
        S3_BUCKET = 'your-s3-bucket-name'
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        GITHUB_TOKEN = credentials('git-token')
        AWS_DEFAULT_REGION    = 'us-west-2' // ID for your AWS credentials in Jenkins
        TAR_FILE = "${DOCKER_IMAGE_NAME}_${DOCKER_TAG}.tar"
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
        stage('Push Docker Image to Docker Hub') {
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
        stage('Save Docker Image as Tarball') {
            steps {
                script {
                    // Save Docker image as a tarball
                    sh "docker save -o ${TAR_FILE} ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                }
            }
        }
        stage('Upload Tarball to S3') {
            steps {
                script {
                    // Configure AWS CLI
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "${AWS_CREDENTIALS_ID"}]]) {
                        // Upload tarball to S3
                        sh "aws s3 cp ${TAR_FILE} s3://${S3_BUCKET}/${TAR_FILE}"
                    }
                }
            }
        }
    }
    post {
        always {
            // Clean up
            sh "rm -f ${TAR_FILE}"
        }
    }
}
