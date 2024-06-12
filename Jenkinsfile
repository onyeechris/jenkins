pipeline {
    agent any

    environment {
        // Docker Hub credentials
        DOCKER_HUB_CREDENTIALS_ID = 'your-docker-hub-credentials-id'
        DOCKER_IMAGE = 'your-docker-repo/your-node-app'
        DOCKER_TAG = 'latest' // or use a variable like "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository
                git 'https://github.com/your-repo/your-node-app.git'
            }
        }
        
        stage('Build and Test in Docker') {
            steps {
                script {
                    docker.image('node:14').inside {
                        // Install dependencies
                        sh 'npm install'
                        
                        // Run tests
                        sh 'npm test'
                        
                        // Build the application
                        sh 'npm run build'
                    }
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}", '.')
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_HUB_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
    }
    
    post {
        always {
            // Clean up workspace
            cleanWs()
        }
        success {
            // Notify success
            echo 'Build, test, and deployment succeeded!'
        }
        failure {
            // Notify failure
            echo 'Build, test, or deployment failed!'
        }
    }
}

