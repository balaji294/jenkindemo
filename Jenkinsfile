pipeline {
    agent any
    stages {
        stage('Build Docker image') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t harsudan/flaskapp:$BUILD_NUMBER -t harsudan/flaskapp:latest .'
            }
        }
        stage('Login to DockerHub') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'test1', 
            usernameVariable: 'DOCKERHUB_USERNAME', 
            passwordVariable: 'DOCKERHUB_PASSWORD'
        )]) {
            echo "Logging in to Docker Hub..."
            sh '''
                echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
            '''
        }
    }
}

        stage('Push image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push harsudan/flaskapp:$BUILD_NUMBER'
                sh 'docker push harsudan/flaskapp:latest'
            }
        }
        stage('Deploy to Staging') {
            steps {
                echo "Deploying container..."
                sh 'docker pull harsudan/flaskapp:latest'
                sh 'docker stop myapp-container || true'
                sh 'docker rm myapp-container || true'
                sh 'docker run -d --name myapp-container -p 8000:8000 harsudan/flaskapp:latest'
            }
        }
    }
    post {
        always {
            echo "Logging out from Docker Hub..."
            sh 'docker logout'
        }
    }
}
