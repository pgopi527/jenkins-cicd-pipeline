pipeline {
    agent any

    stages {
        
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/pgopi527/jenkins-cicd-pipeline.git'
            }
        }
    
        stage('Sonarqube Analysis') {
        environment {
        SONAR_URL = "http://34.201.116.83:9000"
        }
            steps {
                withCredentials([string(credentialsId: 'sonarqube']){
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=jenkins -Dsonar.projectName=jenkins"
                }
            }
        }
    
        stage('Build & Tag Docker Image') {
            steps {
                script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t lasanthasanjeewa/testapp:v1 ."
                   }
                }
            }
        }
    
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format json -o trivy-image-report.json lasanthasanjeewa/testapp:v1"
            }
        }
    
        stage('Push Docker Image') {
            steps {
                script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker push lasanthasanjeewa/testapp:v1 "
                   }
                }
                
            }
        }
    
        stage('Deploy To Container') {
            steps {
                sh "docker run -d -p 8081:80 lasanthasanjeewa/testapp:v1"
            }
        }
    }
}
