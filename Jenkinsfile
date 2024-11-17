pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'ai-front'
        DOCKER_CREDENTIALS = 'soukaina-docker-hub'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git url:"https://github.com/Soukaina235/projet-ai-front-test.git", branch:"main"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:latest")
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Test stage"
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                sh "docker push ${env.dockerHubUser}/${DOCKER_IMAGE_NAME}:latest"
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