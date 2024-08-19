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
                    // Image'i local registry'ye tag'le
                    sh "docker tag my-flask-app localhost:5000/my-flask-app"
                }
            }
        }

        stage('Check if Image Exists in Registry') {
            steps {
                script {
                    def imageExists = sh(script: "curl -s http://localhost:5000/v2/my-flask-app/tags/list | grep -w latest", returnStatus: true)
                    if (imageExists == 0) {
                        echo "Image already exists in registry. Skipping push."
                        currentBuild.result = 'SUCCESS'
                    } else {
                        sh "docker push localhost:5000/my-flask-app"
                    }
                }
            }
        }

        stage('Check if Container is Already Running') {
            steps {
                script {
                    def containerRunning = sh(script: "docker ps -q -f name=flask-app", returnStatus: true)
                    if (containerRunning == 0) {
                        echo "Container is already running. Skipping container creation."
                    } else {
                        dockerImage.run('-d -p 8081:8081 --name flask-app')
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Uygulama calisiyor, su adresten erisebilirsiniz: http://localhost:8081'
        }
    }
}
