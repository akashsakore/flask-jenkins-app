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
        stage('log in to dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                    echo "$PASS" | docker login -u "$USER" --password-stdin
                    """
                }
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
        stage('Run Container') {
            steps {
                sh """
                docker stop flask_app || true
                docker rm flask_app || true
                docker pull ${IMAGE_NAME}:latest
                docker run -d -p 8001:8001 --name flask_app ${IMAGE_NAME}:latest
                """
            }
        }
    }
}
