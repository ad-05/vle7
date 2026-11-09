pipeline {

    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
        IMAGE = 'ad307/vle7-app'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $IMAGE:$BUILD_NUMBER .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKERHUB_PASSWORD" | \
                        docker login -u "$DOCKERHUB_USER" --password-stdin

                        docker push $IMAGE:$BUILD_NUMBER

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml

                    kubectl set image deployment/myapp \
                    myapp=$IMAGE:$BUILD_NUMBER

                    kubectl rollout status deployment/myapp \
                    --timeout=120s
                '''
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }
    }
}
