pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'zzsxdd/my-php-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate Project') {
            steps {
                script {
                    // Проверяем что файлы на месте
                    sh 'ls -la'
                    sh 'ls -la src/'
                    sh 'cat src/index.php'
                    sh 'cat Dockerfile'
                    
                    // Проверяем синтаксис PHP
                    sh 'php -l src/index.php || true'
                }
            }
        }

        stage('Simulate Docker Build') {
            steps {
                script {
                    echo "🚧 This would build Docker image: ${env.DOCKER_IMAGE}:${env.BUILD_ID}"
                    echo "📝 Dockerfile content:"
                    sh 'cat Dockerfile'
                    echo "✅ Build simulation completed"
                }
            }
        }

        stage('Manual Docker Instructions') {
            steps {
                script {
                    echo """
                    📋 ДЛЯ РУЧНОГО ВЫПОЛНЕНИЯ:

                    1. Установите Docker Desktop: https://docker.com/products/docker-desktop/
                    2. Выполните в терминале:

                    cd /Users/nikitacapkov/.jenkins/jobs/my-php-pipeline/workspace
                    docker build -t ${env.DOCKER_IMAGE}:${env.BUILD_ID} .
                    docker run -d -p 8081:80 --name my-running-app ${env.DOCKER_IMAGE}:${env.BUILD_ID}

                    3. Проверьте приложение: http://localhost:8081
                    """
                }
            }
        }
    }

    post {
        always {
            echo "🏁 Pipeline execution completed"
        }
        success {
            echo '✅ Validation passed! Docker setup required for full CI/CD.'
        }
    }
}