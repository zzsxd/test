pipeline {
    agent {
        docker {
            image 'docker:24.0.7'
            args '-v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker'
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

        stage('Build') {
            steps {
                script {
                    sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_ID} .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'docker run --rm ${DOCKER_IMAGE}:${BUILD_ID} php -v'
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        sh 'docker push ${DOCKER_IMAGE}:${BUILD_ID}'
                    }
                }
            }
        }
    }
}