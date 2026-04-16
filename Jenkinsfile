pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        CLUSTER_NAME = "todo-eks-cluster"
    }

    stages {

        stage('Clone K8s Repo') {
            steps {
                git branch: 'main',
                url: 'https://github.com/jayvaja-ecosmob/k8s-todo-app.git'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                kubectl apply -f .
                '''
            }
        }
    }
}