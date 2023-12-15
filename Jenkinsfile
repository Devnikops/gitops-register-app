pipeline {
    agent { label "Jenkins-Agent" }
    environment {
              APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/Devnikops/gitops-register-app'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "nikhil999999"
                   git config --global user.email "nikks.geeks@gmail.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'jenkins-cred', gitToolName: 'Default')]) {
                  sh "git push https://github.com/Devnikops/gitops-register-app main"
                }
            }
        }

        stage('Execute Ansible Playbook') {
            steps {
                script {
                  ansiblePlaybook(credentialsId: 'jenkins-eks',
                                  disableHostKeyChecking: true,
                                  installation: 'Ansible',
                                  inventory: 'hosts',
                                  playbook: 'playbook.yaml'
                                  )
                }
            }
        }
    }
}
