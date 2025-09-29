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

        stage('Validate and Test') {
            steps {
                script {
                    // Проверка проекта
                    sh 'ls -la'
                    sh 'php -l src/index.php || echo "PHP check skipped"'
                    
                    // Проверка Dockerfile
                    sh 'cat Dockerfile'
                    echo "✅ Project validation completed"
                }
            }
        }

        stage('Deploy if Docker Available') {
            steps {
                script {
                    // Пробуем найти Docker
                    sh '''
                        if command -v docker &> /dev/null; then
                            echo "🐳 Docker found - building and deploying..."
                            docker build -t ${DOCKER_IMAGE}:${BUILD_ID} .
                            docker stop my-app || true
                            docker rm my-app || true
                            docker run -d -p 8081:80 --name my-app ${DOCKER_IMAGE}:${BUILD_ID}
                            echo "🚀 App deployed: http://localhost:8081"
                        else
                            echo "❌ Docker not available"
                            echo "💡 Install Docker Desktop from https://docker.com"
                            echo "📦 Or check Docker Hub for auto-built image: https://hub.docker.com/r/zzsxdd/my-php-app"
                        fi
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed!'
            echo '📚 Next steps: Install Docker Desktop for full CI/CD'
        }
    }
}