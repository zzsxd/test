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

        stage('Build Docker Image') {
            steps {
                script {
                    // Пробуем разные возможные пути к Docker
                    sh '''
                        # Пробуем разные расположения Docker
                        if [ -x "/usr/local/bin/docker" ]; then
                            /usr/local/bin/docker build -t ${DOCKER_IMAGE}:${BUILD_ID} .
                        elif [ -x "/opt/homebrew/bin/docker" ]; then
                            /opt/homebrew/bin/docker build -t ${DOCKER_IMAGE}:${BUILD_ID} .
                        elif [ -x "/Applications/Docker.app/Contents/Resources/bin/docker" ]; then
                            /Applications/Docker.app/Contents/Resources/bin/docker build -t ${DOCKER_IMAGE}:${BUILD_ID} .
                        else
                            echo "Docker not found in standard locations"
                            exit 1
                        fi
                    '''
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    sh '''
                        if [ -x "/usr/local/bin/docker" ]; then
                            /usr/local/bin/docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} php -v
                            /usr/local/bin/docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} php -l /var/www/html/index.php
                        elif [ -x "/opt/homebrew/bin/docker" ]; then
                            /opt/homebrew/bin/docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} php -v
                            /opt/homebrew/bin/docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} php -l /var/www/html/index.php
                        fi
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Для пуш используем plugin который может найти Docker
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        // Собираем образ заново через plugin
                        def customImage = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_ID}")
                        customImage.push("${env.BUILD_ID}")
                        customImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Local') {
            steps {
                script {
                    sh '''
                        # Останавливаем старый контейнер
                        if [ -x "/usr/local/bin/docker" ]; then
                            /usr/local/bin/docker stop my-running-app || true
                            /usr/local/bin/docker rm my-running-app || true
                            /usr/local/bin/docker run -d -p 8081:80 --name my-running-app ${DOCKER_IMAGE}:${BUILD_ID}
                        elif [ -x "/opt/homebrew/bin/docker" ]; then
                            /opt/homebrew/bin/docker stop my-running-app || true
                            /opt/homebrew/bin/docker rm my-running-app || true
                            /opt/homebrew/bin/docker run -d -p 8081:80 --name my-running-app ${DOCKER_IMAGE}:${BUILD_ID}
                        fi
                    '''
                    echo "🚀 Application deployed!"
                    echo "📱 Access: http://localhost:8081"
                }
            }
        }
    }

    post {
        always {
            sh '''
                if [ -x "/usr/local/bin/docker" ]; then
                    /usr/local/bin/docker image prune -f
                elif [ -x "/opt/homebrew/bin/docker" ]; then
                    /opt/homebrew/bin/docker image prune -f
                fi
            '''
        }
        success {
            echo '✅ Pipeline succeeded!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}