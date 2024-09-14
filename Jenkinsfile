pipeline {
    agent any

    environment {
        GIT_BRANCH = "${env.BRANCH_NAME}"
    }

    stages {
        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv myenv
                    source myenv/bin/activate
                    pip install docker-compose==1.29.2 docker==4.4.4 urllib3==1.26.6
                '''
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
                        sh '''
                            source myenv/bin/activate
                            docker-compose build
                        '''
                    } catch (Exception e) {
                        error "Docker build failed: ${e.message}"
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
