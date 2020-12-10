@Library('tools@main') _

pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    def inputFile = uploadFile.inputGetFile('public.key')
                    def validKey = sh(script: 'ssh-keygen -l -f public.key', returnStdout: true)
                }
                sh "ls -ltrah"
                sh "echo ${validKey}"
                sh "echo ${validKey.contains('RSA')}"
            }        
        }
        stage("Setup") {
            steps {
                ansiblePlaybook(
                    playbook: 'setup.yml',
                    extraVars: [
                        login: 'user',
                        secret_key: [value: 'password', hidden: true]
                    ])
            }
        }
    }
}
