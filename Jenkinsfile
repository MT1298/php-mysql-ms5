pipeline {
    agent none

    environment {
        BUILD_SERVER = "ec2-user@ip"
        IMAGE_NAME   = "mukeshtho/phpapp:php${BUILD_NUMBER}"
    }

    stages {

        stage('Build PHP Application') {

            agent any

            steps {

                script {

                    sshagent(['slave2']) {

                        withCredentials([
                            usernamePassword(
                                credentialsId: 'dockerhub',
                                usernameVariable: 'USERNAME',
                                passwordVariable: 'PASSWORD'
                            )
                        ]) {

                            echo "Containerising the application"

                            sh "scp -o StrictHostKeyChecking=no -r BuildConfig ${BUILD_SERVER}:/home/ec2-user/"

                            sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER} 'bash /home/ec2-user/BuildConfig/docker-script.sh'"

                            sh "ssh ${BUILD_SERVER} 'docker build -t ${IMAGE_NAME} /home/ec2-user/BuildConfig/'"

                            sh """
                                ssh ${BUILD_SERVER} "echo '${PASSWORD}' | docker login -u '${USERNAME}' --password-stdin"
                            """

                            sh "ssh ${BUILD_SERVER} 'docker push ${IMAGE_NAME}'"
                        }
                    }
                }
            }
        }
    }
}