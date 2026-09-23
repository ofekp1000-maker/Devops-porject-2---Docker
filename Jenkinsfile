pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:24.0.5-dind
    securityContext:
      privileged: true
    env:
    - name: DOCKER_TLS_CERTDIR
      value: ""
  - name: helm
    image: alpine/helm:3.12.3
    command:
    - cat
    tty: true
'''
        }
    }

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
                container('docker') {
                    echo "Waiting for Docker daemon to initialize..."
                    sh "sleep 15"
                    echo "Building the Docker image..."
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'docker-credentials', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        echo "Logging in and Pushing to Docker Hub..."
                        sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                container('helm') {
                    echo "Deploying via Helm..."
                    sh "helm upgrade --install ${RELEASE_NAME} ${CHART_DIR}"
                }
            }
        }
    }
}