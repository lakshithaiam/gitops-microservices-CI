pipeline {
    agent any

    stages {
        stage('Input Docker Tag') {
            steps {
                script {
                    def dockerTagInput = input(
                        id: 'userInput', message: 'Enter Docker Tag', parameters: [
                            string(name: 'DOCKER_TAG', defaultValue: 'latest', description: 'Docker Tag')
                        ]
                    )
                    env.DOCKER_TAG = dockerTagInput
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t lakshithaiam/currencyservice:${env.DOCKER_TAG} ."
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push lakshithaiam/currencyservice:${env.DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Clone and Update k8-manifest.yml') {
            steps {
                script {
                    // Check if the directory exists and delete if it does
                    if (fileExists('gitops-microservices-CD')) {
                        sh 'rm -rf gitops-microservices-CD'
                    }

                    // Use credentials for cloning the repository
                    withCredentials([usernamePassword(credentialsId: 'git-cred', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh 'git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/lakshithaiam/gitops-microservices-CD.git'
                    }

                    // Update k8-manifest.yml with the new Docker tag using sed
                    dir('gitops-microservices-CD') {
                        sh "sed -i 's|image: lakshithaiam/currencyservice:.*|image: lakshithaiam/currencyservice:${env.DOCKER_TAG}|g' Kubernetes_Manifest/k8-manifest.yml"

                        // Configure Git user and commit the changes
                        sh 'git config user.name "lakshithaiam"'
                        sh 'git config user.email "lakshithaiam@gmail.com"'
                        sh 'git add Kubernetes_Manifest/k8-manifest.yml'
                        sh "git commit -m 'Updated Docker image tag to ${env.DOCKER_TAG}'"

                        // Push the changes
                        withCredentials([usernamePassword(credentialsId: 'git-cred', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                            sh 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/lakshithaiam/gitops-microservices-CD.git'
                        }
                    }
                }
            }
        }
    }
}
