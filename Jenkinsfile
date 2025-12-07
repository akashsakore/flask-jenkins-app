pipeline {
    agent {
        label 'docker-node'
    }
    environment {
        IMAGE_NAME = 'akashsakore/flask_app'
        VERSION = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('git checkout') {
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
               sh 'docker build -t ${IMAGE_NAME}:${VERSION} .'
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
                sh """
                docker push ${IMAGE_NAME}:latest
                docker push ${IMAGE_NAME}:${VERSION}
                """
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
    post {
        failure {
            echo "Build failed! Rolling back to previous version..."
            sh """
            PREVIOUS_VERSION=$((${VERSION}-1))

            if [ "$PREVIOUS_VERSION" -le 0 ]; then
                echo "No previous version available — rollback skipped."
                exit 0
            fi
            echo "Rolling back to version: \$PREVIOUS_VERSION"

            docker pull ${IMAGE_NAME}:\$PREVIOUS_VERSION

            docker stop flask_app || true
            docker rm flask_app || true

            docker run -d -p 8001:8001 --name flask_app ${IMAGE_NAME}:\$PREVIOUS_VERSION
            """
        }
        success {
            echo "Deployment successful — no rollback required."
        }
    }
}
