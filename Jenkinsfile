pipeline {
    agent any

    tools {nodejs "nodejs"}

    environment {
        GIT_URL = "https://github.com/Soukaina235/projet-ai-front-test.git"
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
                git url: GIT_URL, branch: "${env.BRANCH_NAME}"
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo "Installing dependencies..."
                    sh 'npm install'
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script { 
                    echo "Running Vitest tests..."
                    sh 'npm test -- --reporter=json > test-output.json --coverage'
                    stash name: 'test-results', includes: 'test-output.json'
                }
            }
        }

        stage('Print JSON content') {
            steps {
                script {
                    sh 'cat ./test-output.json'
                }
            }
        }

        stage("Sonarqube Analysis") {
            environment {
                SCANNER_HOME = tool 'sonar'  // sonar-scanner is the name of the tool in the manage jenkins> tool configuration
            }
            steps {
                withSonarQubeEnv(installationName: 'sonar' , credentialsId: 'sonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner"

                }
            }
        }

        stage('Quality gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false ,  credentialsId: 'sonar'
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    echo "Building and pushing docker image..."
                    def dockerImage = docker.build("${DOCKER_REGISTRY}:${DOCKER_TAG_NAME}")
                    
                    docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS_ID) {
                        dockerImage.push()
                    }
                }
            }
        }


        stage('Deploy') {
            anyOf {
                branch 'main'
                branch 'develop'
            }
            steps {
                script {
                    echo "Deployment stage..."
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