pipeline {
    agent any

    environment {
        IMAGE_NAME = "mevipulsingh/jenkins-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker pull $IMAGE_NAME:$IMAGE_TAG'
                sh 'docker stop jenkins-demo || true'
                sh 'docker rm jenkins-demo || true'
                sh 'docker run -d --name jenkins-demo -p 8081:80 ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }
    }
}
