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
                    // Assign the input value to an environment variable
                    env.DOCKER_TAG = dockerTagInput
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    dir('src'){
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t lakshithaiam/adservice:${env.DOCKER_TAG} ."
                        }
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push lakshithaiam/adservice:${env.DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Clone Repository') {
            steps {
                script {
                    withCredentials([gitUsernamePassword(credentialsId: 'git-cred', gitToolName: 'Default')]) {
                        sh "git clone https://github.com/lakshithaiam/gitops-microservices-CD.git"
                    }
                }
            }
        }

        stage('Update Image Tag in Kubernetes Manifest') {
            steps {
                script {
                    // Use sed to replace the image tag in k8-manifest.yml
                    sh "sed -i 's|image: lakshithaiam/adservice:.*|image: lakshithaiam/adservice:${env.DOCKER_TAG}|' gitops-microservices-CD/Kubernetes_Manifest/k8-manifest.yml"
                }
            }
        }
        
        stage('Commit and Push Changes') {
            steps {
                script {
                    withCredentials([gitUsernamePassword(credentialsId: 'git-cred', gitToolName: 'Default')]) {
                        dir('gitops-microservices-CD') {
                            sh 'git checkout main'
                            sh 'git status'
                            sh 'git config --global user.name "lakshithaiam"'
                            sh 'git config --global user.email "lakshithaiam@gmail.com"'
                            sh 'git add .'
                            sh "git commit -m 'Update image tag to ${env.DOCKER_TAG}'"
                            sh 'git push -u origin main'
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean the workspace at the end
            cleanWs()
        }
    }
}
