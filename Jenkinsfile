pipeline {
    agent any

    stages {

        stage('Clone GitHub Repository') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Sushanta03/Devops-capstone-project2.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t sushzmah/mywebapp:latest .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push sushzmah/mywebapp:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
          steps {
               sh 'kubectl apply -f deployment.yaml'
               sh 'kubectl apply -f service.yaml'
    // Force Kubernetes to pull the latest Docker image
               sh 'kubectl rollout restart deployment/mywebapp-deployment'

    // Wait until the new pods are successfully running
               sh 'kubectl rollout status deployment/mywebapp-deployment'
}
}
        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods -o wide'
                sh 'kubectl get service mywebapp-service'
            }
        }
    }
}
