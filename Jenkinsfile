pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                dir('flask-ci-cd') {
                    git branch: 'main', url: 'https://github.com/akashsakore/flask-ci-cd.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('flask-ci-cd') {
                    script {
                        sh 'docker build --pull -t flask-jenkins .'
                    }
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    // Stop and remove old container if exists
                    sh 'docker stop flask-app || true'
                    sh 'docker rm flask-app || true'
                    // Run new container
                    sh 'docker run -d --name flask-app -p 8001:8001 flask-jenkins'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Build or deployment failed.'
        }
        always {
            script {
                sh 'docker ps -a'
            }
        }
    }
}



