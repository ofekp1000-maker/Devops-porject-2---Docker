pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ofekp002/my-python-app"
        RELEASE_NAME = "my-release"
        CHART_DIR = "./python-app-chart"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo "Building the Docker image..."
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo "Pushing the image to Docker Hub..."
                sh "docker push ${DOCKER_IMAGE}:latest"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying via Helm..."
                sh "helm upgrade --install ${RELEASE_NAME} ${CHART_DIR}"
            }
        }
    }
}