pipeline {
    agent any
    environment {
        // Docker image and registry configuration
        DOCKER_IMAGE = "surjeetm/matchmaking" // Your Docker image name
        DOCKER_REGISTRY = "hub.docker.com" // Docker registry (e.g., Docker Hub, ECR, etc.)
        DOCKER_TAG = "latest" // Docker image tag (e.g., latest or commit hash)
    }
    tools{
        maven "m3"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/surjeetm/maven-web-app.git'
            }
        }
        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Docker Image') {
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
        stage('k8s deployment') {
            steps {
                sh 'kubectl apply -f k8s-deploy.yml'
            }
        }
    }
}
