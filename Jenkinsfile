@Library('tools@main') _

parameters {
        string(name: 'user_to_add', defaultValue: 'bob', description: 'user to create')
        string(name: 'filename', defaultValue: 'bob', description: 'user to create')
        string(name: 'root_username', defaultValue: 'user', description: 'Jenkins privilege user')
        password(name: 'root_password', defaultValue: 'password', description: 'Jenkins privilege password')
    }

pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    def inputFile = uploadFile.inputGetFile(params.filename)
                    validKey = sh(script: 'ssh-keygen -l -f ' + params.filename, returnStdout: true)
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
                        login: params.root_username,
                        secret_key: [value: params.root_password, hidden: true],
                        filepath: params.filename,
                        user_to_add: params.user_to_add
                    ])
            }
        }
        stage ("Test Ansible from shell") {
            steps {
                sh """
                ansible-playbook
                """
            }
        }
    }
}
