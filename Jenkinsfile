pipeline {
    agent any

    triggers {
        githubPush() // This triggers the pipeline on every push to GitHub
    }

    environment {
        DOCKERHUB_USER = 'praneeth2611'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Build Java App') {
            steps {
                script {
                    echo 'Building Java app...'
                    bat '''
                    docker run --rm -v "%CD%\\java-app":/app -w /app maven:3.9.6-eclipse-temurin-17 mvn clean package
                    '''
                }
            }
        }

        stage('Build Go App') {
            steps {
                script {
                    echo 'Building Go app...'
                    bat '''
                    docker run --rm -v "%CD%\\go-app":/app -w /app golang:1.22 go build -o myapp.exe
                    '''
                }
            }
        }

        stage('Build PHP App') {
            steps {
                script {
                    echo 'Building PHP app...'
                    bat '''
                    docker run --rm -v "%CD%\\php-app":/app -w /app php:8.3-cli php -v
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Java Docker Image') {
                    steps {
                        script {
                            echo 'Building Java Docker image...'
                            bat "docker build -t %DOCKERHUB_USER%/java-app ./java-app"
                        }
                    }
                }
                stage('Build Go Docker Image') {
                    steps {
                        script {
                            echo 'Building Go Docker image...'
                            bat "docker build -t %DOCKERHUB_USER%/go-app ./go-app"
                        }
                    }
                }
                stage('Build PHP Docker Image') {
                    steps {
                        script {
                            echo 'Building PHP Docker image...'
                            bat "docker build -t %DOCKERHUB_USER%/php-app ./php-app"
                        }
                    }
                }
            }
        }

        stage('Push Docker Images to DockerHub') {
            steps {
                script {
                    echo 'Pushing Docker images to DockerHub...'
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                        bat "docker push %DOCKERHUB_USER%/java-app"
                        bat "docker push %DOCKERHUB_USER%/go-app"
                        bat "docker push %DOCKERHUB_USER%/php-app"
                        bat 'docker logout'
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
