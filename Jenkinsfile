pipeline {
    agent any

    environment {
        IMAGE_NAME = "your-dockerhub-username/demo"
        K8S_DEPLOYMENT = "demo-deployment"
        K8S_NAMESPACE = "default"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_PASS')]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u your-dockerhub-username --password-stdin
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                    docker push $IMAGE_NAME:$BUILD_NUMBER
                """
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                sh """
                    sed -i 's|IMAGE_PLACEHOLDER|$IMAGE_NAME:$BUILD_NUMBER|g' deployment.yaml
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                """
            }
        }
    }
}

