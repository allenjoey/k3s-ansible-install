/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any

    triggers {
        pollSCM ''
    }

    stages {
        stage('Verify Branch') {
            steps {
                echo "$GIT_BRANCH"
            }
        }
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
                recipients: 'your_email@your_domain'])
        }
    }
}
