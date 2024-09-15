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
                    # Create a virtual environment using python3
                    python3 -m venv ${VENV_DIR}

                    # Activate the virtual environment and install dependencies using pip3
                    . ${VENV_DIR}/bin/activate

                    # Ensure we are in the correct directory where requirements.txt is located
                    cd ${env.WORKSPACE}

                    # Install dependencies
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
        stage('Run Unit Tests') {
            steps {
                script {
                    try {
                        sh '''
                        # Activate the virtual environment and run unit tests using python3
                        . ${VENV_DIR}/bin/activate

                        # Ensure we are in the correct directory where tests are located
                        cd ${env.WORKSPACE}

                        # Run unit tests
                        python3 -m unittest discover -s tests
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
