pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REGISTRY = '533267023710.dkr.ecr.us-east-1.amazonaws.com'
        ECR_REPOSITORY = 'automation_repo'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/jaimin-vitthalpara/DockerImageAutomation.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${ECR_REPOSITORY}:${IMAGE_TAG}")
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    dockerImage.push("${IMAGE_TAG}")
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
