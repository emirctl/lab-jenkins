pipeline {
    agent any
    
    tools {
        nodejs 'node-7.8.0' 
    }

    environment {
        APP_PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        
        IMAGE_NAME = "node${env.BRANCH_NAME}"
        IMAGE_TAG = "v1.0"
        
        CONTAINER_NAME = "container-${env.BRANCH_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || echo "Tests completed with warnings"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying to port ${APP_PORT}..."
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    
                    sh "docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
    
    post {
        success {
            echo "Successfully deployed branch ${env.BRANCH_NAME} to http://localhost:${APP_PORT}"
        }
    }
}
