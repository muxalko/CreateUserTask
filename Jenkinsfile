@Library('tools@main') _

pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    def inputFile = uploadFile.inputGetFile('public.key')
                    //validKey = sh(script: 'ssh-keygen -l -f public.key', returnStdout: true)
                }
                sh "ls -ltrah"
                sh "ssh-keygen -l -f public.key"
                //sh "echo ${validKey.contains('RSA')}"
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
