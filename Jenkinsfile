pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'ai-front'
        DOCKER_CREDENTIALS = 'soukaina-docker-hub'
    }

    stages {
        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                        sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
                    }
                }
            }
        }
        
        stage('Git Checkout') {
            steps {
                git url: "https://github.com/Soukaina235/projet-ai-front-test.git", branch: "main"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${dockerHubUser}/${DOCKER_IMAGE_NAME}:latest")
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
