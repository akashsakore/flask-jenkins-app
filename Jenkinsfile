pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t flask-jenkins .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh 'docker stop flask-app || true'
                    sh 'docker rm flask-app || true'
                    sh 'docker run -d --name flask-app -p 8001:8001 flask-jenkins'
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Cleaning up or logging if needed'
                sh 'docker ps -a'
            }
        }
    }
}
