pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'zzsxdd/my-php-app'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/zzsxd/test.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // На macOS может потребоваться явно указать демон Docker
                    dockerImage = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Для простого PHP-приложения можно запустить базовые проверки
                    dockerImage.inside("--rm") {
                        sh 'php -v'
                        sh 'php -l /var/www/html/index.php'
                    }
                }
            }
        }

        stage('Push Docker Image') {
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
                    // Останавливаем и удаляем старый контейнер
                    sh '''
                        docker stop my-php-app || true
                        docker rm my-php-app || true
                    '''
                    // Запускаем новый контейнер
                    sh """
                        docker run -d -p 8081:80 --name my-php-app ${env.DOCKER_IMAGE}:${env.BUILD_ID}
                    """
                    echo "Приложение развернуто и доступно по http://localhost:8081"
                }
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f'
        }
        success {
            echo 'Pipeline успешно завершен!'
            echo 'Приложение доступно по: http://localhost:8081'
        }
        failure {
            echo 'Pipeline завершился с ошибкой!'
        }
    }
}