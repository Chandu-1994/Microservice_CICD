pipeline {

    agent any

    environment {
        IMAGE = "YOUR_DOCKER_USERNAME/payment-service"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm $IMAGE:$TAG python -c "import flask; print(\"Test Passed\")"'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE:$TAG'
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl set image deployment/payment-service payment-service=$IMAGE:$TAG'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl rollout status deployment/payment-service'
            }
        }
    }
}