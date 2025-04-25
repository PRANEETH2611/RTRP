pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials-id'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo.git'  // Checkout code from GitHub
            }
        }

        stage('Build Java App') {
            steps {
                sh 'mvn clean install'  // Build Java app (Maven)
            }
        }

        stage('Build Go App') {
            steps {
                sh 'docker run --rm -v "$PWD:/app" -w /app golang:1.22 go build -o myapp.exe'  // Build Go app
            }
        }

        stage('Build PHP App') {
            steps {
                sh 'docker run --rm -v "$PWD:/app" -w /app php:8.3-cli php -v'  // Build PHP app
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Java Docker Image') {
                    steps {
                        script {
                            docker.build('praneeth2611/java-app', './java-app')  // Build Docker image for Java
                        }
                    }
                }
                stage('Build Go Docker Image') {
                    steps {
                        script {
                            docker.build('praneeth2611/go-app', './go-app')  // Build Docker image for Go
                        }
                    }
                }
                stage('Build PHP Docker Image') {
                    steps {
                        script {
                            docker.build('praneeth2611/php-app', './php-app')  // Build Docker image for PHP
                        }
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials-id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker push praneeth2611/java-app'
                        sh 'docker push praneeth2611/go-app'
                        sh 'docker push praneeth2611/php-app'
                    }
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                script {
                    // Use Docker or Docker Compose to deploy locally
                    sh 'docker-compose up -d'  // Or use docker run commands for each container
                }
            }
        }

        stage('Post-Deployment Test') {
            steps {
                script {
                    // Run tests to check if the app is deployed correctly (e.g., curl for health check)
                    sh 'curl http://localhost:8080/actuator/health'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
            // Send success notification (e.g., email or Slack)
        }
        failure {
            echo 'Deployment Failed!'
            // Send failure notification (e.g., email or Slack)
        }
    }
}
