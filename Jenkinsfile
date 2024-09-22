pipeline {
    agent any
    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
        DOCKERHUB_CREDENTIALS = credentials('78362e3a-8762-448c-8f62-00bb32b681cb') // Replace with your Jenkins credentials ID
        DOCKERHUB_USERNAME = "${DOCKERHUB_CREDENTIALS_USR}" // Use the username from the credentials
        DOCKERHUB_REPO = 'suyeshmathur/jenkins' // Replace with your Docker Hub repository name
        IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        IMAGE_NAME = "mcr.microsoft.com/azuredocs/azure-vote-front:v1" // Set the image name from docker-compose.yml
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
                    sh '/usr/local/lib/docker/cli-plugins/docker-compose build'
                }
            }
        }
        stage('Tag Docker Image') {
            steps {
                script {
                    echo "Tagging image: ${IMAGE_NAME} as ${DOCKERHUB_USERNAME}/${DOCKERHUB_REPO}:${IMAGE_TAG}"
                    sh "docker tag ${IMAGE_NAME} ${DOCKERHUB_USERNAME}/${DOCKERHUB_REPO}:${IMAGE_TAG}"
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    sh '''
                    echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                    '''
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    sh '''
                    docker push ${DOCKERHUB_USERNAME}/${DOCKERHUB_REPO}:${IMAGE_TAG}
                    '''
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
