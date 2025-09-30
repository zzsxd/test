pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'zzsxdd/my-php-app'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        PATH = "/opt/homebrew/bin:${env.PATH}"
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
    }
    
    post {
        always {
            echo 'Pipeline completed'
        }
    }
}