pipeline {
    agent any
    environment {
        AWS_REGION = "us-east-1"
        CLUSTER_NAME = "todo-eks-cluster"
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Clone K8s Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jayvaja-ecosmob/k8s-todo-app.git'
            }
        }
        stage('Deploy to EKS') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                        aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME
                        kubectl apply -f .
                    '''
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment to EKS succeeded!'
        }
        failure {
            echo 'Deployment to EKS failed!'
        }
    }
}