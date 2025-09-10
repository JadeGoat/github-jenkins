pipeline {
    agent any

    environment {
        REPO_USERNAME = credentials("REPO_USERNAME")
        DOCKER_NAME = "jenkins-demo"
        CONTAINER_NAME = "temp-container"
        HOST_PORT = 8080
        DOCKER_PORT = 80
    }   

    stages {
        stage("Build Docker Image") {
            steps {
                echo "Building Docker Image..."
                bat "docker build -t ${REPO_USERNAME}/${DOCKER_NAME} ."
                echo "Build Completed"
            }
        }
        stage("Running container") {
            steps {
                // 1. Pre-cleanup (in case pipeline fails)
                echo "Preparation..."
                script {
                    // a. Retrieve containerId if exist
                    echo "Preparing ingredient 1...."
                    def containerId = bat(
                        script: "docker ps -aq --filter name=${CONTAINER_NAME}",
                        returnStdout: true
                    ).trim()
                    
                    // b. Remove container if exist
                    echo "Preparing ingredient 2....."
                    if (containerId) {
                        bat "docker rm -f ${CONTAINER_NAME}"
                    } else {
                        echo "Container "${CONTAINER_NAME}" does not exist. Skipping removal."
                    }

                    // 2. Running & Testing container
                    echo "Running container..."
                    bat "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${DOCKER_PORT} ${REPO_USERNAME}/${DOCKER_NAME}"
                    echo "Run Completed"

                    echo "Testing container..."
                    bat "curl --connect-timeout 10 http://localhost:${HOST_PORT}"
                }
            }
        }
         stage("Push Docker Image") {
            steps {
                // Push to registry when running container is ok
                echo "Pushing Docker Image..."
                withCredentials([string(credentialsId: "DOCKER_PASSWORD", variable: "DOCKER_PASSWORD")]) {
                    bat "docker login -u ${REPO_USERNAME} -p %DOCKER_PASSWORD%"
                    bat "docker push ${REPO_USERNAME}/${DOCKER_NAME}"
                    bat "docker logout"
                }
                echo "Push Completed"
            }
        }
        stage("Clean up Container & Cache") {
            steps {
                // Cleanup container
                echo "Cleaning unused container..."
                bat "docker stop ${CONTAINER_NAME}"
                bat "docker rm ${CONTAINER_NAME}"
                echo "Clean Completed"

                // Cleanup image cache
                echo "Cleaning unused image cache..."
                bat "docker rmi ${REPO_USERNAME}/${DOCKER_NAME}"
                bat "docker system prune --force"
                echo "Clean Completed"
            }
        }
        stage("Deploy") {
            steps {
                echo "Deploying..."
                // Pull from registry to deploy
            }
        }
    }
}