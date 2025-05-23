pipeline {
    agent any

    environment {
        IMAGE_NAME = 'pavanthumati/java-hello-app'
        TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
                sh 'ls -l target'

            }
        }

        stage('Docker Build and Push') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    withDockerRegistry([credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/']) {
                        def image = docker.build("${IMAGE_NAME}:${TAG}")
                        image.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'develop'
            }
            steps {
                //withCredentials([file(credentialsId: 'kubeconfig-cred-id', variable: 'KUBECONFIG')])
                 withKubeConfig([credentialsId: 'kubeconfig-cred'])
                {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                    echo 'completed'
                }
            }
        }
    }
}
