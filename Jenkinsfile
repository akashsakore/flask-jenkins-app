pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-ci-cd:4"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/akashsakore/flask-ci-cd.git'
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
                    sh 'docker stop flask-app || true && docker rm flask-app || true'
                    sh 'docker run -d --name flask-app -p 8001:8001 flask-jenkins'
                }
            }
        }
    }
}

