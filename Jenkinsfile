pipeline {

    agent any

    environment {
        // Fix 1: Added missing opening double quote
        IMAGE = "chanduuser1/payment-service"
        TAG = "${BUILD_NUMBER}"
        // Fix 2: Set Kubeconfig so kubectl runs successfully under Jenkins user
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh '''
                    python3 --version
                    echo "Running application validation"
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t $IMAGE:$TAG .
                    docker tag $IMAGE:$TAG $IMAGE:latest
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USER" --password-stdin

                        docker push $IMAGE:$TAG
                        docker push $IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    kubectl set image deployment/payment-service \
                    payment-service=$IMAGE:$TAG
                '''
            }
        }

        stage('Verify Rollout') {
            steps {
                sh '''
                    kubectl rollout status deployment/payment-service
                '''
            }
        }
    }
}
