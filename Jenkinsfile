pipeline {
    agent any

    environment {
        KUBECONFIG = '/home/ec2-user/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("nidhinb143/webapp:latest")
                    // Store the Docker image ID for later use
                    dockerImageId = dockerImage.id
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'nidhinb143') {
                        // Use the Docker image ID previously stored
                        docker.image(dockerImageId).push('latest')
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    sh 'kubectl config set-cluster minikube --server=https://127.0.0.1:8443 --insecure-skip-tls-verify=true --kubeconfig=/home/ec2-user/.kube/config'
                    sh 'kubectl config set-context minikube --cluster=minikube --user=minikube'
                    sh 'kubectl config use-context minikube'
                    sh 'kubectl --kubeconfig=${KUBECONFIG} apply -f deployment.yaml'
                    sh 'kubectl --kubeconfig=${KUBECONFIG} apply -f service.yaml'
                }
            }
        }
    }
}
