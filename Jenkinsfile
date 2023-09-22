pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'https://registry.hub.docker.com/v2/ '
        DOCKER_IMAGE_NAME = 'demo-kubernetes'
        KUBE_NAMESPACE = 'jenkin'
        KUBE_DEPLOYMENT_NAME = 'jenkin-deployment'
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
                     // Define the image name and tag
                    def imageName = "demo-kubernetes"
                    def imageTag = "latest"
                    def dockerImage = "${imageName}:${imageTag}"

                    // Build the Docker image
                    sh "docker build -t ${dockerImage} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://${DOCKER_REGISTRY}', 'docker-registry-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl config set-context --current --namespace=${KUBE_NAMESPACE}"
                    sh "kubectl set image deployment/${KUBE_DEPLOYMENT_NAME} ${KUBE_DEPLOYMENT_NAME}=${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
