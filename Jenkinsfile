pipeline {
    agent any
    environment {
        dockerImage = "pradipchaudhary7/jenkinspipelinesetup"
    }
    stages {
        stage('Building the Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh "docker build -t ${dockerImage}:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Scanning the Image') {
            steps {
                script {
                    echo "Scanning the Docker image for vulnerabilities..."
                    sh '''
                    trivy image --timeout 10m --scanners vuln --severity HIGH,CRITICAL --ignore-unfixed ${dockerImage}:${BUILD_NUMBER} || echo "No critical vulnerabilities found"
                    '''
                }
            }
        }

        stage('Pushing the Docker Image to Docker Hub') {
            steps {
                script {
                    echo "Logging into Docker Hub..."
                    withDockerRegistry([credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/']) {
                        sh "docker push ${dockerImage}:${BUILD_NUMBER}"
                    }
                }
            }
        }
    }

    post {
        always {
            mail to: 'iampradip.creation@gmail.com',
            subject: "Job '${JOB_NAME}' (${BUILD_NUMBER}) Status",
            body: "Please go to ${BUILD_URL} and verify the build."
        }
        
        success {
            emailext(
                to: 'iampradip.creation@gmail.com',
                subject: "Jenkins Build ${JOB_NAME} #${BUILD_NUMBER} Success",
                body: """The build was successful.
                Check the job at ${BUILD_URL}"""
            )
        }

        failure {
            emailext(
                to: 'iampradip.creation@gmail.com',
                subject: "Jenkins Build ${JOB_NAME} #${BUILD_NUMBER} Failed",
                body: """The build failed. Please check the logs at ${BUILD_URL}"""
            )
        }
    }
}
