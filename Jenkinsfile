pipeline {
    agent none

    environment {
        BUILD_SERVER = 'ec2-user@172.31.13.218'
        IMAGE_NAME = "mukeshtho/phpapp:php{BUILD_NUMBER}"
    }
    stages{
        stage ('Build the php application') {
            agent any 
            steps {
                script {
                    sshagent(['slave2']) {
                        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')])
                        echo "Containerising the application"
                        sh "scp -o StrictHostKeyChecking=no -r BuildConfig ${BUILD_SERVER}:/home/ec2-user/"
                        sh "ssh -o StrictHostKeyChecking=no -i ${BUILD_SERVER} 'bash /home/ec2-user/BuildConfig/docker-script.sh'"
                        sh "ssh ${BUILD_SERVER} 'docker build -t ${IMAGE_NAME} /home/ec2-user/BuildConfig/'"
                        sh "ssh ${BUILD_SERVER} 'docker login -u ${USERNAME} -p ${PASSWORD}'"
                        sh "ssh ${BUILD_SERVER} 'docker push ${IMAGE_NAME}'"
                        }

                    }
                }
            }

        }

    }

}

