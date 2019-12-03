// Jenkinsfile
import groovy.json.JsonOutput

String credentialsId = 'awsCredentials'

try {
  stage('checkout') {
    node {
      cleanWs()
      checkout scm
    }
  }

  // Run terraform init
  stage('init') {
    node {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        script {
            def tfHome = tool name: 'Terraform'
            env.PATH = "${tfHome}:${env.PATH}"
        }
        ansiColor('xterm') {
          sh "terraform --version"
          sh 'terraform init'
        }
      }
    }
  }

  // Run terraform plan
  stage('plan') {
    node {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh 'terraform plan'
        }
      }
    }
  }

   stage('approve_notification') {
     node {
        office365ConnectorSend message: "Do you approve deployment? " + env.JOB_UR, status:"SUCCESS", webhookUrl:'https://outlook.office.com/webhook/a0f1d097-a33c-4109-ab36-73d4c945fb5c@71ad2f62-61e2-44fc-9e85-86c2827f6de9/JenkinsCI/f8eba51747c24d228397ffba6305c907/16de5a6e-4f56-40b3-8359-ee0ac3075bb9'
        // script {
        //    env.TERRAFORM_APPROVE = input message: 'User input required',
        //       parameters: [choice(name: 'Tag on Docker Hub', choices: 'no\nyes', description: 'Choose "yes" if you want to deploy this build')]
        // }
     }
   }

    // stage('approve') {
    //   when {
    //     environment name: 'TERRAFORM_APPROVE', value: 'yes'
    //   }
    //    node {
    //     withCredentials([[
    //       $class: 'AmazonWebServicesCredentialsBinding',
    //       credentialsId: credentialsId,
    //       accessKeyVariable: 'AWS_ACCESS_KEY_ID',
    //       secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
    //     ]]) {
    //       ansiColor('xterm') {
    //         sh 'terraform apply -auto-approve'
    //         sh 'terraform show'
    //       }
    //     }
    //   }
    // }

     // Run terraform destroy
    // stage('destroy') {
    //   node {
    //     withCredentials([[
    //       $class: 'AmazonWebServicesCredentialsBinding',
    //       credentialsId: credentialsId,
    //       accessKeyVariable: 'AWS_ACCESS_KEY_ID',
    //       secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
    //     ]]) {
    //       ansiColor('xterm') {
    //         sh 'terraform destroy'
    //       }
    //     }
    //   }
    // }

/*
  if (env.BRANCH_NAME == 'master') {
    // Run terraform apply
    stage('apply') {
      node {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh 'terraform apply -auto-approve'
          }
        }
      }
    }

    // Run terraform show
    stage('show') {
      node {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh 'terraform show'
          }
        }
      }
    }
  }
*/
  currentBuild.result = 'SUCCESS'
}
catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}