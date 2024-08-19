pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MNecati/jenkins-deneme.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("my-flask-app")
                    // Image'i local registry'e tag'le
                    sh "docker tag my-flask-app localhost:5000/my-flask-app"
                }
            }
        }

        stage('Push to Local Registry') {
            steps {
                // Image'i local registry'e push et
                sh "docker push localhost:5000/my-flask-app"
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    dockerImage.run('-d -p 8081:8081 --name flask-app')
                }
            }
        }
    }

    post {
        always {
            echo 'Uygulama çalışıyor, şu adresten erişebilirsiniz: http://localhost:8081'
        }
    }
}
