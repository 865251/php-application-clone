pipeline {
    agent any

    environment {
        ECR_REPO_NAME = 'my-php-application'
        AWS_ACCOUNT_ID = '891377339969'
        AWS_REGION = 'us-east-1' // change if different
        IMAGE_TAG = '1.0'
        ECR_IMAGE = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Code Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                git 'https://github.com/865251/php-application-clone.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "sudo docker build -t ${ECR_IMAGE} ."
            }
        }

        stage('Login to ECR') {
            steps {
                echo 'Logging into Amazon ECR...'
                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
            }
        }

        stage('Push to ECR') {
            steps {
                echo 'Pushing Docker image to ECR...'
                sh "sudo docker push ${ECR_IMAGE}"
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying Docker container from ECR image...'
                sh "sudo docker run -d -p 8081:80 ${ECR_IMAGE}"
            }
        }
    }
}
