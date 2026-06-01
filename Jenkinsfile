pipeline {
    agent any

    environment {
        // Name of our docker image
        IMAGE_NAME = 'ofbiz-docker'
        // Port mapping
        PORT_MAPPING = '8443:8443'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the polled GitHub repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building the Docker image..."
                    // Execute the docker build command
                    sh 'docker build --tag ${IMAGE_NAME} .'
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                script {
                    echo "Stopping and removing existing container (if any)..."
                    sh '''
                        docker stop ${IMAGE_NAME} || true
                        docker rm ${IMAGE_NAME} || true
                    '''

                    echo "Starting the new container..."
                    // Using -d to run in detached mode (background)
                    sh 'docker run -d -e OFBIZ_DATA_LOAD=demo --name ${IMAGE_NAME} -p ${PORT_MAPPING} ${IMAGE_NAME}'
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! OFBiz is running at https://localhost:8443/partymgr"
        }
        failure {
            echo "Pipeline failed! Please check the logs."
        }
    }
}
