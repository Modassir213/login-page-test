pipeline {
    agent any

    environment {
        IMAGE = "modassirkhan213/login-page"
        TAG = "latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE}:${TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        dockerImage.push("${TAG}")
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {    // ✔️ Correct credential ID
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@3.108.236.212 "
                            docker pull modassirkhan213/login-page:latest &&
                            docker stop login || true &&
                            docker rm login || true &&
                            docker run -d --name login -p 80:80 modassirkhan213/login-page:latest
                        "
                    '''
                }
            }
        }

    }
}
