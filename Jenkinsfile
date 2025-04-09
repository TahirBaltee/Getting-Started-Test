pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials'
        DOCKER_HUB_REPO = 'tahirbaltee'
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
                        bat """
                        kubectl config set-cluster k8s-cluster --server=https://10.160.0.4:6443 --insecure-skip-tls-verify=true
                        kubectl config set-credentials jenkins-user --token=%KUBE_TOKEN%
                        kubectl config set-context jenkins-context --cluster=k8s-cluster --user=jenkins-user
                        kubectl config use-context jenkins-context
                        kubectl apply -f deployment.yaml --validate=false
                        kubectl apply -f service.yaml --validate=false
                        """
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    bat """
                    kubectl get pods
                    kubectl get services
                    """
                }
            }
        }

    }
}
