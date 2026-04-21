def sendGoogleChatNotification(message) {
    withCredentials([string(credentialsId: 'gchat-webhook', variable: 'WEBHOOK_URL')]) {
        sh """
        curl -X POST -H 'Content-Type: application/json' \
        -d '{
              "text": "${message}"
            }' \
        "$WEBHOOK_URL"
        """
    }
}

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
                        kubectl apply -f namespace.yaml
                        kubectl apply -f .
                    '''
                }
            }
        }
    }

    post {
        success {
            script {
                sendGoogleChatNotification("🚀 SUCCESS: EKS Deployment completed for ${env.JOB_NAME} #${env.BUILD_NUMBER}")
            }
            echo 'Deployment to EKS succeeded!'
        }

        failure {
            script {
                sendGoogleChatNotification("❌ FAILURE: EKS Deployment failed for ${env.JOB_NAME} #${env.BUILD_NUMBER}")
            }
            echo 'Deployment to EKS failed!'
        }
    }
}