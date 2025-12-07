pipeline {
    agent {
        label 'docker-node'
    }
    environment {
        IMAGE_NAME = 'akashsakore/flask_app'
    }
    stages {
        stage('git-clone') {
            steps {
                git branch: 'main', url: 'https://github.com/akashsakore/flask-jenkins-app.git'
            }
        }
        stage('delete old local image') {
            steps {
                echo 'deleting old local images'
                sh 'docker image prune -af || true'
            }
        }
        stage('Build docker image') {
            steps {
               sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }
    }
}
