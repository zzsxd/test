pipeline {
    agent {
        docker {
            image 'docker:24.0.7'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
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
                    dockerImage = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
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

        stage('Deploy to Local') {
            steps {
                script {
                    sh """
                        docker stop my-running-app || true
                        docker rm my-running-app || true  
                        docker run -d -p 8081:80 --name my-running-app ${env.DOCKER_IMAGE}:${env.BUILD_ID}
                    """
                    echo "🚀 Application deployed!"
                }
            }
        }
    }
    
    post {
        always {
            sh 'docker image prune -f'
        }
    }
}