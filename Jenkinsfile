pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'jayakumar110821' // Replace with your Docker Hub username
        IMAGE_NAME = 'abstergo-webstore' // Choose a name for your Docker image
        K8S_NAMESPACE = 'default' // Kubernetes namespace to deploy to
        DEPLOYMENT_NAME = 'abstergo-webstore-deployment' // Name of your Kubernetes Deployment
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Jayakumar110821/cicd-pipeline-jenkins-complete.git'
            }
        }

        stage('Build and Test') {
            steps {
                // Add your build and test commands here
                // For a simple web application, this might involve:
                // sh 'npm install'
                // sh 'npm run build'
                echo 'Building and testing application...'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${BUILD_NUMBER}", '.')
                    dockerImage.push()
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubernetesDeploy(
                        configs: "k8s/deployment.yaml, k8s/service.yaml", // Path to your Kubernetes YAML files
                        namespace: "${K8S_NAMESPACE}"
                    )
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
