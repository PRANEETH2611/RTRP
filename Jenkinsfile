pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'praneeth2611' // 🔁 Replace with your actual DockerHub username
        DOCKERHUB_CREDENTIALS = credentials('docker-creds') // 🔐 Jenkins credentials ID
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
                            docker.build("${DOCKERHUB_USER}/java-app", './java-app')
                        }
                    }
                }
                stage('Build Go Docker Image') {
                    steps {
                        script {
                            docker.build("${DOCKERHUB_USER}/go-app", './go-app')
                        }
                    }
                }
                stage('Build PHP Docker Image') {
                    steps {
                        script {
                            docker.build("${DOCKERHUB_USER}/php-app", './php-app')
                        }
                    }
                }
            }
        }

        stage('Push Docker Images to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        docker.image("${DOCKERHUB_USER}/java-app").push()
                        docker.image("${DOCKERHUB_USER}/go-app").push()
                        docker.image("${DOCKERHUB_USER}/php-app").push()
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                sh 'docker logout'
            }
        }
    }
}
