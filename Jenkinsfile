pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'zzsxdd/my-php-app'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    // Используем docker.build из Docker Pipeline plugin
                    dockerImage = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_ID}")
                    
                    // Тестируем образ
                    dockerImage.inside {
                        sh 'php -v'
                        sh 'apache2 -v'
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push("${env.BUILD_ID}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker stop my-php-app || true'
                    sh 'docker rm my-php-app || true'
                    sh "docker run -d -p 8080:80 --name my-php-app ${env.DOCKER_IMAGE}:${env.BUILD_ID}"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}