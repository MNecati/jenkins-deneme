pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                // GitHub repository'nizi klonluyoruz
                git 'https://github.com/MNecati/jenkins-deneme.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Docker image oluşturuyoruz
                    def image = docker.build("jenkins-deneme:latest")
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Docker container çalıştırıyoruz
                    docker.image("jenkins-deneme:latest").run('-d -p 5000:5000')
                }
            }
        }
    }
}
