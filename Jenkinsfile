pipeline {
    agent any

    environment {
        // Define your Docker image name
        DOCKER_IMAGE = 'robi050993/my-web-app:latest'
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Fetch SonarQube token from Jenkins credentials
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Check out the code from GitHub
                git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/robi3484/devops.git'
            }
        }

        stage('SAST - SonarQube Scan') {
            steps {
                script {
                    withSonarQubeEnv('sonar-qube') {
                        sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=my-web-app \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                // Push the Docker image to Docker Hub using credentials
                withDockerRegistry([credentialsId: 'docker-hub-creds', url: '']) {
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Apply the Kubernetes deployment manifest
                sh 'microk8s kubectl apply -f deployment.yaml --validate=false'
            }
        }
    }
}

