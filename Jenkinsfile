pipeline {
    agent any

    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/arifsadiq/valentines-devops-lab.git'
            }
        }
        stage('Trivy Filesystem Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fs-report.html .'
            }
        }
        stage('SonarQube Scanner') {
            steps {
                withSonarQubeEnv('sonar') {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=valentine -Dsonar.projectKey=valentine '''
                }
            }
        }
        stage('Docker Build and Tag') {
            steps {
                script {
                   withDockerRegistry(credentialsId: 'docker-cred1', toolName: 'docker') {
                      sh 'docker build -t ari786/valentine-html .'
                      sh 'docker tag ari786/valentine-html ari786/valentine-html:latest'
                   }
                }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image ari786/valentine-html:latest > trivy-image-result.txt'
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred1', toolName: 'docker') {
                       sh 'docker push ari786/valentine-html:latest'
                   }
                }
            }
        }
    }
}