pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
        DOCKER_HUB_REPO = 'tahirbaltee'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {  // <-- Added "stages" block to wrap all the stage definitions

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/TahirBaltee/Getting-Started-Test.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_HUB_REPO}/getting-started:latest", ".")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIALS) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Using the Kubernetes token from Jenkins credentials
                withCredentials([string(credentialsId: 'kubernetes-token', variable: 'KUBE_TOKEN')]) {
                    // Export the KUBECONFIG and apply the deployments
                sh 'export KUBECONFIG=/home/tahirsalingi/.kube/config'
                sh 'kubectl apply -f deployment.yaml --token=$KUBE_TOKEN'
                sh 'kubectl apply -f service.yaml --token=$KUBE_TOKEN'
                    
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([string(credentialsId: 'kubernetes-token', variable: 'KUBE_TOKEN')]) {
                    sh 'kubectl get pods --token=$KUBE_TOKEN'
                    sh 'kubectl get services --token=$KUBE_TOKEN'
                }
            }
        }

    } // <-- Properly closed the "stages" block
}
