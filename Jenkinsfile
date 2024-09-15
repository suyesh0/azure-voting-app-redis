pipeline {
    agent any

    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
        VENV_DIR = "${env.WORKSPACE}/venv"
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    sh '''
                    # Ensure we are in the correct directory where requirements.txt is located
                    cd ${env.WORKSPACE}

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
