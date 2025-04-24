pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-creds')
    }

    stages {
        stage('Build Java App') {
            steps {
                dir('java-app') {
                    script {
                        docker.image('maven:3.9.6-eclipse-temurin-17').inside {
                            sh 'mvn clean package'
                        }
                    }
                }
            }
        }

        stage('Build Go App') {
            steps {
                dir('go-app') {
                    script {
                        docker.image('golang:1.22').inside {
                            sh 'go build -o myapp'
                        }
                    }
                }
            }
        }

        stage('Build PHP App') {
            steps {
                dir('php-app') {
                    script {
                        docker.image('php:8.3-cli').inside {
                            sh 'php -v'
                        }
                    }
                }
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Java Docker Image') {
                    steps {
                        script {
                            docker.build('your-dockerhub-username/java-app', './java-app')
                        }
                    }
                }
                stage('Build Go Docker Image') {
                    steps {
                        script {
                            docker.build('your-dockerhub-username/go-app', './go-app')
                        }
                    }
                }
                stage('Build PHP Docker Image') {
                    steps {
                        script {
                            docker.build('your-dockerhub-username/php-app', './php-app')
                        }
                    }
                }
            }
        }

        stage('Push Docker Images to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'your-dockerhub-credentials-id') {
                        docker.image('your-dockerhub-username/java-app').push()
                        docker.image('your-dockerhub-username/go-app').push()
                        docker.image('your-dockerhub-username/php-app').push()
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
