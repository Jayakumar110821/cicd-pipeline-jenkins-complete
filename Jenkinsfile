pipeline {
    agent any

    environment {
        DOCKER_REGISTRY_URL = 'https://registry.hub.docker.com'
        DOCKER_USERNAME     = credentials('dockerhub-credentials').username
        DOCKER_PASSWORD     = credentials('dockerhub-credentials').password
        IMAGE_NAME          = 'jayakumar110821/abstergo-webstore'
        IMAGE_TAG           = "${BUILD_NUMBER}"
        FULL_IMAGE_NAME     = "${DOCKER_REGISTRY_URL}/${IMAGE_NAME}:${IMAGE_TAG}"
        K8S_NAMESPACE       = 'default' // Adjust this to your Kubernetes namespace
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Checkout') {
            steps {
                git credentialsId: 'dockerhub-credentials', url: 'https://github.com/Jayakumar110821/cicd-pipeline-jenkins-complete.git'
            }
        }

        stage('Build and Test') {
            steps {
                echo 'Building and testing application...'
                // Add your build and test commands here
                // Example for a Node.js application:
                sh 'npm install'
                sh 'npm test'
            }
        }

       stage('Build Docker Image') {
        steps {
            script {
                isUnix() {
                    withEnv(['DOCKER_BUILDKIT=1']) {
                        sh "docker build -t '${IMAGE_NAME}:${IMAGE_TAG}' ."
                    }
                } else {
                    bat "docker build -t \"%IMAGE_NAME%:%IMAGE_TAG%\" ."
                }
            }
        }
    }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-credentials', url: "${DOCKER_REGISTRY_URL}") {
                    script {
                        isUnix() {
                            sh "docker tag '${IMAGE_NAME}:${IMAGE_TAG}' '${FULL_IMAGE_NAME}'"
                            sh "docker push '${FULL_IMAGE_NAME}'"
                        } else {
                            bat "docker tag \"%IMAGE_NAME%:%IMAGE_TAG%\" \"%FULL_IMAGE_NAME%\""
                            bat "docker push \"%FULL_IMAGE_NAME%\""
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubernetesDeploy(
                        configs: 'k8s/deployment.yaml, k8s/service.yaml',
                        namespace: "${K8S_NAMESPACE}"
                    )
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Optional: Add cleanup steps here
        }
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
