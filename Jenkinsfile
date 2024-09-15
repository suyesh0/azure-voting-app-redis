pipeline {
    agent any

    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
        VENV_DIR = "${env.WORKSPACE}/venv"
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
                    # Print the current directory and list files
                    echo "Current directory: $(pwd)"
                    echo "Listing files:"
                    ls -la

                    # Ensure we are in the correct directory where requirements.txt is located
                    cd ${env.WORKSPACE}

                    # Print the current directory and list files again
                    echo "Current directory after cd: $(pwd)"
                    echo "Listing files after cd:"
                    ls -la

                    # Create a virtual environment using python3
                    python3 -m venv ${VENV_DIR}

                    # Activate the virtual environment and install dependencies using pip3
                    . ${VENV_DIR}/bin/activate
                    pip3 install -r requirements.txt
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
                        sh 'docker-compose build'
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
                        sh 'docker-compose up -d'
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
                        sh 'docker-compose down'
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
