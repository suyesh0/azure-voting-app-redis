groovy
pipeline {
    agent any
    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id') // Replace with your Jenkins credentials ID
        DOCKERHUB_USERNAME = 'your-dockerhub-username' // Replace with your Docker Hub username
        DOCKERHUB_REPO = 'your-dockerhub-repo' // Replace with your Docker Hub repository name
        IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository
                checkout scm
            }
        }
        stage('Setup') {
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    set -e
                    # Print the current directory and list files
                    echo "Current directory: $(pwd)"
                    echo "Listing files:"
                    ls -la
                    # Ensure we are in the correct directory
                    cd ${WORKSPACE}
                    # Print the current directory and list files again
                    echo "Current directory after cd: $(pwd)"
                    echo "Listing files after cd:"
                    ls -la
                    '''
                }
            }
        }
        stage('Verify Branch') {
            steps {
                echo "Current Git Branch: ${GIT_BRANCH}"
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    try {
                        sh 'sudo /usr/local/lib/docker/cli-plugins/docker-compose build'
                    } catch (Exception e) {
                        error "Docker build failed: ${e.message}"
                    }
                }
            }
        }
        stage('Tag Docker Image') {
            steps {
                script {
                    try {
                        // Extract the image name from the docker-compose.yml file
                        def imageName = sh(script: "grep 'image:' docker-compose.yml | awk '{print \$2}'", returnStdout: true).trim()
                        if (imageName) {
                            sh "sudo docker tag ${imageName} ${DOCKERHUB_USERNAME}/${DOCKERHUB_REPO}:${IMAGE_TAG}"
                        } else {
                            error "Image name not found in docker-compose.yml"
                        }
                    } catch (Exception e) {
                        error "Tagging Docker image failed: ${e.message}"
                    }
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    try {
                        sh '''
                        echo ${DOCKERHUB_CREDENTIALS_PSW} | sudo docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                        '''
                    } catch (Exception e) {
                        error "Docker login failed: ${e.message}"
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    try {
                        sh '''
                        sudo docker push ${DOCKERHUB_USERNAME}/${DOCKERHUB_REPO}:${IMAGE_TAG}
                        '''
                    } catch (Exception e) {
                        error "Pushing Docker image failed: ${e.message}"
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
