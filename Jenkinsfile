@Library('tools@main') _

env.filename = 'public.key'
env.user_to_add = 'bob'


pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    
                    def inputFile = uploadFile.inputGetFile(env.filename)
                    validKey = sh(script: 'ssh-keygen -l -f ' + env.filename, returnStdout: true)
                }
                sh """
                echo "Valid key provided: " ${validKey.contains('RSA')}
                """
            }        
        }
        stage("Setup") {
            steps {
                ansiblePlaybook(
                    playbook: 'setup.yml',
                    extraVars: [
                        login: 'user',
                        secret_key: [value: 'password', hidden: true],
                        filepath: env.filename
                        user_to_add: env.user_to_add
                    ])
            }
        }
    }
}
