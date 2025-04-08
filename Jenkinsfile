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
                        bat 'kubectl --token=%KUBE_TOKEN% --server=https://10.160.0.4:6443 --insecure-skip-tls-verify apply -f deployment.yaml'
                        bat 'kubectl --token=%KUBE_TOKEN% --server=https://10.160.0.4:6443 --insecure-skip-tls-verify apply -f service.yaml'
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    bat 'kubectl --token=%KUBE_TOKEN% --server=https://10.160.0.4:6443 --insecure-skip-tls-verify get pods'
                    bat 'kubectl --token=%KUBE_TOKEN% --server=https://10.160.0.4:6443 --insecure-skip-tls-verify get services'
                }
            }
        }

    } // Close stages block
} // Close pipeline block
