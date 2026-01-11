pipeline {
    agent {
        node {
            label 'manager'
        }
    }

    tools {
        maven 'maven'
    }

    stages {

        stage('CODE') {
            steps {
                git 'https://github.com/GovardhanReddyA/python-code-library-app.git'
            }
        }

        stage('docker-build') {
            steps {
                sh 'docker build -t dbimage database'
                sh 'docker build -t authimage auth'
                sh 'docker build -t bookimage book'
                sh 'docker build -t borrowimage borrow'
                sh 'docker build -t frontendimage .'
            }
        }

        stage('scan image') {
            steps {
                sh 'trivy image dbimage'
                sh 'trivy image authimage'
                sh 'trivy image bookimage'
                sh 'trivy image borrowimage'
                sh 'trivy image frontendimage'
            }
        }

        stage('tag image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub') {
                        sh 'docker tag dbimage govardhan7989/dbimage:latest'
                        sh 'docker tag authimage govardhan7989/authimage:latest'
                        sh 'docker tag bookimage govardhan7989/bookimage:latest'
                        sh 'docker tag borrowimage govardhan7989/borrowimage:latest'
                        sh 'docker tag frontendimage govardhan7989/frontendimage:latest'
                    }
                }
            }
        }

        stage('docker push & deploy') {
            steps {
                sh 'docker stack deploy -c compose.yaml gova'
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
        always {
            echo "📦 Pipeline finished"
        }
    }
}

