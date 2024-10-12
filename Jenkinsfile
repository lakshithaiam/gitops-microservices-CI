pipeline {
    agent any

    stages {
        stage('Initialize Terraform') {
            steps {
                script {
                    dir('terraform') {
                        sh "terraform --version"
                        sh "terraform init"
                    }
                }
            }
        }
        stage('Plan Infrastructure Changes') {
            steps {
                script {
                    dir('terraform') {
                        sh "terraform plan"
                    }
                    input(message: "Are you sure you want to proceed with applying the infrastructure changes?", ok: "Proceed")
                }
            }
        }
        stage('Manage Infrastructure State') {
            steps {
                script {
                    dir('terraform') {
                        def action = input message: 'Select Action', parameters: [
                            choice(name: 'ACTION', choices: ['apply', 'destroy'], description: 'Choose whether to apply or destroy the infrastructure')
                        ]

                        if (action == 'apply') {
                            sh 'terraform apply --auto-approve'
                        } else if (action == 'destroy') {
                            sh 'terraform destroy --auto-approve'
                        }
                    }
                }
            }
        }
        stage('Deploy Ecommerce Web Application') {
            steps {
                script {
                    dir('configuration') {
                        sh 'aws eks update-kubeconfig --name myapp-eks-cluster'
                        sh 'kubectl apply -f deployment-service.yml'
                    }
                }
            }
        }
    }
}
