pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
        DOCKER_HUB_REPO = 'tahirbaltee'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {

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
                withCredentials([string(credentialsId: 'kubernetes-token', variable: 'KUBE_TOKEN')]) {
                    script {
                        bat 'export KUBECONFIG=/home/tahirsalingi/.kube/config'
                        bat 'kubectl apply -f deployment.yaml'
                        bat 'kubectl apply -f service.yaml'
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    bat 'kubectl get pods'
                    bat 'kubectl get services'
                }
            }
        }

    } // Close stages block
} // Close pipeline block
