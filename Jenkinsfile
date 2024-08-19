pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Kaynak kodu bu repodan çek
                git url: 'https://github.com/MNecati/jenkins-deneme.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Docker imajını oluştur
                script {
                    dockerImage = docker.build("my-flask-app")
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                // Docker konteynerını başlat
                script {
                    dockerImage.run('-d -p 8081:8081 --name flask-app')
                }
            }
        }
    }

    post {
        always {
            // Uygulama çalıştıktan sonra mesajı göster
            echo 'Uygulama çalışıyor, şu adresten erişebilirsiniz: http://localhost:8081'
        }
    }
}
