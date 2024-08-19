pipeline {
    agent any

    environment {
        // Kubeconfig dosyanızın yolunu belirtin
        KUBECONFIG = '/var/jenkins_home/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                // Git reposunu çekme
                git branch: 'main', url: 'https://github.com/MNecati/jenkins-deneme.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Docker image'ı oluşturma
                    dockerImage = docker.build("my-flask-app")
                    // Image'i local registry'ye tag'leme
                    sh "docker tag my-flask-app 192.168.232.127:5000/my-flask-app:latest"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Docker image'ın registry'de olup olmadığını kontrol etme
                    def imageExists = sh(script: "curl -s http://192.168.232.127:5000/v2/my-flask-app/tags/list | grep -w latest", returnStatus: true)
                    if (imageExists == 0) {
                        echo "Image already exists in registry. Skipping push."
                    } else {
                        // Docker image'ı registry'ye push etme
                        sh "docker push 192.168.232.127:5000/my-flask-app:latest"
                    }
                }
            }
        }

        stage('Create Deployment File') {
            steps {
                script {
                    // Deployment manifest dosyasını oluşturma
                    sh '''
                    echo "
                    apiVersion: apps/v1
                    kind: Deployment
                    metadata:
                      name: flask-app
                      labels:
                        app: flask-app
                    spec:
                      replicas: 1
                      selector:
                        matchLabels:
                          app: flask-app
                      template:
                        metadata:
                          labels:
                            app: flask-app
                        spec:
                          containers:
                          - name: flask-app
                            image: 192.168.232.127:5000/my-flask-app:latest
                            ports:
                            - containerPort: 8081
                    " > deployment.yaml
                    '''
                    // YAML dosyasının içeriğini kontrol etme
                    sh 'cat deployment.yaml'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Kubernetes'e deploy etme
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            // Pipeline işlemi tamamlandığında kullanıcıya bilgi verme
            echo 'Deployment işlemi tamamlandı. Uygulama http://192.168.232.127:8081 adresinden erişilebilir.'
        }
    }
}
