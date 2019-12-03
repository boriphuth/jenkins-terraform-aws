pipeline {
 agent any
 
 stages {

        stage('checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Setup Terraform') {
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

        stage('Terraform init') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: credentialsId,
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    ansiColor('xterm') {
                    sh 'terraform init'
                    }
                }
            }
        }
        // stage('Provision infrastructure') {
        //     steps {
        //         ansiColor('xterm') {
        //             sh 'terraform init'
        //             sh 'terraform plan -out=plan'
        //             // sh ‘terraform destroy -auto-approve’
        //             sh 'terraform apply plan'
        //         }
        //     }
        // }
    }
}
