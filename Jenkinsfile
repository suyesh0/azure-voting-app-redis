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
        stage('Run Unit Tests') {
            steps {
                script {
                    try {
                        sh '''
                        #!/bin/bash
                        set -e

                        # Ensure we are in the correct directory
                        cd ${WORKSPACE}

                        # Run unit tests using unittest
                        sudo python3 -m unittest discover -s tests
                        '''
                    } catch (Exception e) {
                        error "Unit tests failed: ${e.message}"
                    }
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    try {
                        sh 'sudo docker-compose build'
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
                        sh 'sudo docker-compose up -d'
                    } catch (Exception e) {
                        error "Starting application failed: ${e.message}"
                    }
                }
            }
        }
        stage('Stop Application') {
            steps {
                script {
                    try {
                        sh 'sudo docker-compose down'
                    } catch (Exception e) {
                        error "Stopping application failed: ${e.message}"
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
