pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image'
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application to Kubernetes'
                sh 'sudo /usr/local/bin/deploy-to-minikube'
            }
        }

        stage('Verify') {
            steps {
                echo 'Checking Kubernetes deployment'
                sh 'sudo -u ubuntu /usr/bin/kubectl get pods'
                sh 'sudo -u ubuntu /usr/bin/kubectl get services'
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check Jenkins logs.'
        }
    }
}
