pipeline {
    agent any
    environment {
        dockerImage = "pradipchaudhary7/jenkinspipelinesetup"
    }
    stages {
        stage('Building the image') {
            steps {
                script {
                    sh "docker image build -t ${dockerImage}:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Scanning the image') {
            steps {
                script {
                    // Scan the built image with Trivy for high and critical vulnerabilities
                    sh 'trivy image --timeout 10m --scanners vuln --exit-code 1 --severity HIGH,CRITICAL --ignore-unfixed $dockerImage:$BUILD_NUMBER'
                }
            }
        }

        stage('Pushing the docker image to the docker hub') {
            steps {
                script {
                    // Push the Docker image to Docker Hub after the build and scan
                    withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
                        sh '''
                        docker push ${dockerImage}:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            mail to: 'iampradip.creation@gmail.com',
            subject: "Job '${JOB_NAME}' (${BUILD_NUMBER}) status",
            body: "Please go to ${BUILD_URL} and verify the build"
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
