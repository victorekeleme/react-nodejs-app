#!usr/bin/env groovy

library identifier:'jenkins-shared-library@jenkins-npm', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/victorekeleme/jenkins-shared-library.git',
    credentialsId: 'git-credentials'
    ])

pipeline {
    agent any

    environment {
        IMAGE_NAME = 'vistein12/react-nodejs-app:1.1'
    }
    stages{
        stage("Build Image") {
            steps {
                script {
                    echo "Building Docker Image"
                    dockerLogin()
                    dockerBuildImage(env.IMAGE_NAME)
                    dockerPushImage(env.IMAGE_NAME)
                }
            }
        }

        stage("Deploy on AWS") {
            steps {
                script {
                    // def dockerCmd = "docker run -d -p 3000:3080 ${env.IMAGE_NAME}"
                    def dockerComposeCmd = "docker-compose -f docker-compose.yaml up --detach"
                    echo "Deploying to AWS EC2 Instance"
                    sshagent(['ec2-credentials']) {
                        sh "scp ./docker-compose.yaml ec2-user@18.117.146.90:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@18.117.146.90 ${dockerComposeCmd}"
                        
                    }
                }
            }
        }
    }
}