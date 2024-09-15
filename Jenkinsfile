pipeline {
    agent any

    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    sh '''
                    # Create a virtual environment using python3
                    python3 -m venv /home/ubuntu/venv

                    # Activate the virtual environment and install dependencies using pip3
                    . /home/ubuntu/venv/bin/activate
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
                        . /home/ubuntu/venv/bin/activate
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
