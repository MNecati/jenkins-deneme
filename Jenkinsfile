pipeline {
    agent any

    environment {
        KUBECONFIG = '/var/jenkins_home/.kube/config'
    }

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
                    sh "docker tag my-flask-app localhost:5000/my-flask-app:latest"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def imageExists = sh(script: "curl -s http://localhost:5000/v2/my-flask-app/tags/list | grep -w latest", returnStatus: true)
                    if (imageExists == 0) {
                        echo "Image already exists in registry. Skipping push."
                    } else {
                        sh "docker push localhost:5000/my-flask-app:latest"
                    }
                }
            }
        }

        stage('Create Deployment File') {
            steps {
                script {
                    sh '''
                    cat <<EOF > deployment.yaml
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
                            image: localhost:5000/my-flask-app:latest
                            ports:
                            - containerPort: 8081
                    EOF
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            echo 'Deployment işlemi tamamlandı. Uygulama http://localhost:8081 adresinden erişilebilir.'
        }
    }
}
