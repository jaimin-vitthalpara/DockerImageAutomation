pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REGISTRY = '533267023710.dkr.ecr.us-east-1.amazonaws.com'
        ECR_REPOSITORY = 'automation_repo'
        IMAGE_TAG = 'latest'
        DOCKER_HOST = 'tcp://192.168.56.33:2375'  // Your Docker VM IP
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[
                        url: 'https://github.com/jaimin-vitthalpara/DockerImageAutomation.git',
                        credentialsId: 'Github'
                    ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withEnv(["DOCKER_HOST=tcp://192.168.56.33:2375"]) {
                        sh '''
                            echo "Docker Host: $DOCKER_HOST"
                            docker version
                            docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} .
                        '''
                    }
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    withEnv(["DOCKER_HOST=tcp://192.168.56.33:2375"]) {
                        sh '''
                            aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                        '''
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withEnv(["DOCKER_HOST=tcp://192.168.56.33:2375"]) {
                        sh '''
                            docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}
                            docker push ${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after the build
        }
    }
}
