pipeline {
    agent any

    environment {
        GIT_URL = "https://github.com/Soukaina235/projet-ai-front-test.git"
        DOCKER_IMAGE = 'ai-front'
        DOCKER_TAG_NAME = 'latest'
        DOCKER_REGISTRY = 'soukaina915/ai-front'
        DOCKER_REGISTRY_CREDENTIALS_ID = 'soukaina-docker-hub' 
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
            steps {
                echo "Cloning code..."
                git url: GIT_URL, branch: "main"
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo "Installing dependencies..."
                    //sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running tests..."
                    //sh 'npm test'
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    echo "Building and pushing docker image..."
                    docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS_ID) {
                        DOCKER_IMAGE = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG_NAME}")
                        DOCKER_IMAGE.push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deployment stage"
                }
            }
        }
    }

    post {
        always {
            // Cleanup workspace after build
            cleanWs()
        }
        success {
            // Actions on successful deployment
            echo 'Deployment successful!'
        }
        failure {
            // Actions on failed deployment
            echo 'Deployment failed!'
        }
    }
}