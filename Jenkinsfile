pipeline {
    agent any  // This means the pipeline will run on any available Jenkins agent

    environment {
        // Define environment variables like the Docker image name and tag
        DOCKER_IMAGE = "tobimadariola/youtube-clone"
        DOCKER_TAG = "1.0.0"
        REGISTRY_CREDENTIALS = "docker-hub-credentials"  // Jenkins credentials for Docker Hub login
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from the Git repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    // Run the container and test if it's working (for example, by checking if the app starts)
                    // This runs the container for a simple health check (e.g., checking the Node.js version)
                    sh "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} node -v"
                    
                    // You could add more testing steps to ensure the application works as expected, like:
                    // sh "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} curl -f http://localhost:3000"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub using Jenkins credentials
                    withCredentials([usernamePassword(credentialsId: REGISTRY_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }

                    // Push the Docker image to Docker Hub
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    // Remove the image after the push to free up space on Jenkins agents
                    sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        // Clean up any resources or take actions after the pipeline has run (success or failure)
        always {
            // You could remove any containers or images that are left over
            sh "docker system prune -f"
            echo "ALL DONE!!!"
        }
    }
}
