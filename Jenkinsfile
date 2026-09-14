pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'nourz1/nodejs-docker-exercise'
        CREDENTIALS_ID = 'docker'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
            
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${env.BRANCH_NAME}-${env.BUILD_NUMBER} -t ${DOCKER_IMAGE}:latest ."
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}:${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
                        sh "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
