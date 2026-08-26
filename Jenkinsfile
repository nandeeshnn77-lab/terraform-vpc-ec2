pipeline {

    agent {
        label 'Devops-Worker'
    }

    environment {
        TF_IN_AUTOMATION = 'true'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Tools') {
            steps {
                sh '''
                    set -e

                    echo "===== Terraform ====="
                    terraform version

                    echo "===== AWS ====="
                    aws --version

                    echo "===== AWS Identity ====="
                    aws sts get-caller-identity
                '''
            }
        }

        stage('Terraform Init') {
            steps {
                sh '''
                    set -e

                    terraform init
                '''
            }
        }

        stage('Terraform Format') {
            steps {
                sh '''
                    set -e

                    terraform fmt -check -recursive
                '''
            }
        }

        stage('Terraform Validate') {
            steps {
                sh '''
                    set -e

                    terraform validate
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                sh '''
                    set -e

                    terraform plan \
                        -out=tfplan
                '''
            }
        }

        stage('Approval') {
            steps {
                input message: 'Apply Terraform infrastructure?',
                      ok: 'Apply'
            }
        }

        stage('Terraform Apply') {
            steps {
                sh '''
                    set -e

                    terraform apply \
                        -auto-approve \
                        tfplan
                '''
            }
        }

        stage('Terraform Output') {
            steps {
                sh '''
                    terraform output
                '''
            }
        }
    }

    post {

        success {
            echo 'Terraform infrastructure created successfully'
        }

        failure {
            echo 'Terraform pipeline failed'
        }
    }
}
