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
                git url: GIT_URL, branch: "main"
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
                    sh 'npm test'

                    // sh 'npm run test -- --reporter=vitest-junit-reporter'
                    // junit '**/test-results/*.xml'
                }
            }
        }

        stage('Parse and Display JSON Results') {
            steps {
                script {
                    def jsonResult = readJSON file: '**/test-output.json'

                    // Print out summary info
                    echo "Total Tests: ${jsonResult.numTotalTests}"
                    echo "Passed: ${jsonResult.numPassedTests}"
                    echo "Failed: ${jsonResult.numFailedTests}"

                    // Loop through individual tests and display their status
                    jsonResult.testResults.each { testResult ->
                        testResult.assertionResults.each { assertion ->
                            echo "Test: ${assertion.fullName}"
                            echo "Status: ${assertion.status}"
                            echo "Duration: ${assertion.duration} ms"
                        }
                    }
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