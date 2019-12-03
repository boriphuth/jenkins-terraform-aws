pipeline {
 agent any
 
 stages {

        stage('checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Set Terraform path') {
            steps {
                script {
                def tfHome = tool name: 'Terraform'
                env.PATH = "${tfHome}:${env.PATH}"
                }
                ansiColor('xterm') {
                    sh 'terraform -v'
                }
            }
        }

        stage('Provision infrastructure') {
            steps {
                ansiColor('xterm') {
                    sh 'terraform init'
                    sh 'terraform plan -out=plan'
                    // sh ‘terraform destroy -auto-approve’
                    sh 'terraform apply plan'
                }
            }
        }
    }
}
