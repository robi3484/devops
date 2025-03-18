pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'robi050993/my-web-app:latest'
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Fetch SonarQube token from Jenkins credentials
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/robi3484/devops.git'
            }
        }

        stage('SAST - SonarQube Scan') {
            steps {
                script {
                    withSonarQubeEnv('sonar-qube') {
                        sh '''
                        docker run --rm \
                        -e SONAR_HOST_URL=http://172.17.0.2:9000 \
                        -e SONAR_LOGIN=$SONAR_TOKEN \
                        -v $(pwd):/usr/src \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey=my-web-app \
                        -Dsonar.sources=/usr/src
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-creds', url: '']) {
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'microk8s kubectl apply -f deployment.yaml --validate=false'
            }
        }
    }
}

