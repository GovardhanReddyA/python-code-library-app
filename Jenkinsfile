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
        emailext(
            subject: "SUCCESS: Job ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """
                <h2>Build Successful</h2>
                <p><b>Job:</b> ${env.JOB_NAME}</p>
                <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                <p><b>URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
            """,
            to: "yourmail@gmail.com",
            mimeType: 'text/html'
        )
    }

    failure {
        emailext(
            subject: "FAILED: Job ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """
                <h2>Build Failed</h2>
                <p><b>Job:</b> ${env.JOB_NAME}</p>
                <p><b>Build:</b> ${env.BUILD_NUMBER}</p>
                <p><b>Check Console:</b> <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
            """,
            to: "agrr7989@gmail.com",
            mimeType: 'text/html'
        )
    }
}

}

