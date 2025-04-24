pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'praneeth2611'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Build Java App') {
            steps {
                dir('java-app') {
                    bat 'mvn clean package'
                }
            }
        }

        stage('Build Go App') {
            steps {
                dir('go-app') {
                    bat 'go build -o myapp.exe'
                }
            }
        }

        stage('Build PHP App') {
            steps {
                dir('php-app') {
                    bat 'php -v'
                }
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Java Docker Image') {
                    steps {
                        bat "docker build -t %DOCKERHUB_USER%/java-app ./java-app"
                    }
                }
                stage('Build Go Docker Image') {
                    steps {
                        bat "docker build -t %DOCKERHUB_USER%/go-app ./go-app"
                    }
                }
                stage('Build PHP Docker Image') {
                    steps {
                        bat "docker build -t %DOCKERHUB_USER%/php-app ./php-app"
                    }
                }
            }
        }

        stage('Push Docker Images to DockerHub') {
            steps {
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
