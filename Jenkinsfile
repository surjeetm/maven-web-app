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
			withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials' , usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
			     
                    }
                }
            }
        }   
        stage('Push Docker Image') {
            steps {
                 sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
}
