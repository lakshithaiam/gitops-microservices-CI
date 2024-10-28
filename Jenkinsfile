pipeline {
    agent any

    stages {
        stage('Input Docker Tag') {
            steps {
                script {
                    // Prompt for the Docker tag and store it in a variable
                    def dockerTagInput = input(
                        id: 'userInput', message: 'Enter Docker Tag', parameters: [
                            string(name: 'DOCKER_TAG', defaultValue: 'latest', description: 'Docker Tag')
                        ]
                    )
                    // Assign the input value to a variable
                    env.DOCKER_TAG = dockerTagInput
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t lakshithaiam/checkoutservice:${env.DOCKER_TAG} ."
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push lakshithaiam/checkoutservice:${env.DOCKER_TAG}"
                    }
                }
            }
        }
    }
}
