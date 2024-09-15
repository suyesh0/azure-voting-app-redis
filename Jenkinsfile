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
                    # Example setup commands
                    # Uncomment and modify the following lines as needed
                    # python3 -m venv venv
                    # source venv/bin/activate
                    # pip install -r requirements.txt
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
                        # Example unit test command
                        # Replace the following line with your actual unit test command
                        # For example, for Node.js: npm test
                        # For example, for Maven: mvn test
                        # For example, for Python (if not using pytest): python -m unittest discover
                        npm test
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
