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

        stage('Build') {
            steps {
                script {
                    // Используем Docker с хоста
                    sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_ID} .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} php -v'
                    // Дополнительные тесты, если нужно
                    sh 'docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} apache2 -v'
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        sh 'docker push ${DOCKER_IMAGE}:${BUILD_ID}'
                        // Также пушим latest
                        sh 'docker tag ${DOCKER_IMAGE}:${BUILD_ID} ${DOCKER_IMAGE}:latest'
                        sh 'docker push ${DOCKER_IMAGE}:latest'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Останавливаем и удаляем старый контейнер
                    sh 'docker stop my-php-app || true'
                    sh 'docker rm my-php-app || true'
                    // Запускаем новый контейнер
                    sh 'docker run -d -p 8080:80 --name my-php-app ${DOCKER_IMAGE}:${BUILD_ID}'
                }
            }
        }
    }

    post {
        always {
            // Очистка
            sh 'docker system prune -f || true'
        }
        success {
            echo 'Pipeline succeeded!'
            echo 'Application deployed at: http://localhost:8080'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}