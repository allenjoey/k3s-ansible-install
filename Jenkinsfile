/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any

    triggers {
        pollSCM 'H/5 * * * *'
    }

    stages {
        stage('Execute Ansible Playbook') {
            steps {
                ansiblePlaybook credentialsId: 'private-key',
                                 disableHostKeyChecking: true,
                                 installation: 'Ansible',
                                 inventory: 'inventory',
                                 playbook: 'site.yml'
            }
        }
    }

    post {
        always {
            step([$class: 'Mailer', notifyEveryUnstableBuild: true,
                recipients: 'your-email@email.com'])
        }
    }
}
