```groovy
pipeline {
    agent any

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'dev',
                    changelog: false,
                    url: 'https://github.com/divyanayanaa/lms-webapp.git'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'

                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=lms-webapp \
                            -Dsonar.projectName=lms-webapp \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/**,.next/**,out/**,build/**,coverage/**,.git/**
                        """
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', url: 'https://index.docker.io/v1/') {
                        sh 'docker build -t lms-webapp -f Dockerfile .'
                        sh 'docker tag lms-webapp divyanayanaa/lms-webapp:latest'
                        sh 'docker push divyanayanaa/lms-webapp:latest'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop lms-container || true
                    docker rm lms-container || true
                    docker run -d --name lms-container -p 3000:3000 divyanayanaa/lms-webapp:latest
                '''
            }
        }
    }
}
```
