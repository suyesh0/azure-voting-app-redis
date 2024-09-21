pipeline {
    agent any
    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
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
                        sh '/usr/local/lib/docker/cli-plugins/docker-compose build'
                    } catch (Exception e) {
                        error "Docker build failed: ${e.message}"
                    }
                }
            }
        }
        stage('Start Application') {
            steps {
                script {
                    try {
                        sh '/usr/local/lib/docker/cli-plugins/docker-compose up -d'
                    } catch (Exception e) {
                        error "Starting application failed: ${e.message}"
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
