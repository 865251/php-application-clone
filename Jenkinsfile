pipeline {
    agent any

    environment {
        ECR_REPO_NAME = 'my-php-application'
        AWS_ACCOUNT_ID = '891377339969'
        AWS_REGION = 'us-east-1'
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
                sh "docker build -t ${ECR_IMAGE} ."
            }
        }

        stage('Login to ECR') {
            steps {
                echo 'Logging into Amazon ECR...'
                withCredentials([usernamePassword(credentialsId: 'aws-ecr-creds', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    script {
                        sh """
                            export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                            export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                            aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        """
                    }
                }
            }
        }

        stage('Push to ECR') {
            steps {
                echo 'Pushing Docker image to ECR...'
                sh "docker push ${ECR_IMAGE}"
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying Docker container from ECR image...'
                sh "docker run -d -p 8081:80 ${ECR_IMAGE}"
            }
        }
    }
}
