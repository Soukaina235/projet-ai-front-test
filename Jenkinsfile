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
