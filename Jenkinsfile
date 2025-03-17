pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'robi050993/my-web-app'
        KUBE_CONFIG = '/root/.kube/config'  // Path to kubeconfig
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-creds', url: 'https://github.com/robi3484/devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-creds', url: '']) {
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/my-app my-app=$DOCKER_IMAGE --kubeconfig=$KUBE_CONFIG
                '''
            }
        }
    }
}

