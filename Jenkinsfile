pipeline {
    agent any

    environment {
        // Docker image and registry configuration
        DOCKER_IMAGE = "surjeetm/matchmaking" // Your Docker image name
        DOCKER_REGISTRY = "hub.docker.com" // Docker registry (e.g., Docker Hub, ECR, etc.)
        DOCKER_TAG = "latest" // Docker image tag (e.g., latest or commit hash)
        K8S_NAMESPACE = "your-namespace" // Kubernetes namespace where the app is deployed
        K8S_DEPLOYMENT_NAME = "your-deployment" // Kubernetes deployment name to update
        GIT_REPO_URL = "https://github.com/surjeetm/maven-web-app.git" // Git repository URL
        GIT_BRANCH = "main" // Git branch to build from
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {  
                    // Clone the Git repository
                    git url: GIT_REPO_URL, branch: GIT_BRANCH
		 }
                }
            }
        }

        stage('Build Maven Project') {
            steps {
                script {
                    // Run Maven to build the project (skip tests for faster build)
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image from the Dockerfile in the repo
                    sh "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to the Docker registry
                    withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                        sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Update Kubernetes Deployment') {
            steps {
                script {
                    // Update the Kubernetes deployment's container image in the deployment.yaml
                    sh """
                    sed -i 's|image: .*|image: ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}|' k8s/deployment.yaml
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }


