@Library('tools@main') _

env.public_key_filepath = 'public.key'
env.user_to_add = 'bob'
 
pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    def inputFile = uploadFile.inputGetFile(env.public_key_filepath)
                    validKey = sh(script: 'ssh-keygen -l -f ' + env.public_key_filepath, returnStdout: true)
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
                    //become: true,
                    //becomeUser: '{{ root_username }}',
                    colorized: true,
                    //disableHostKeyChecking: true,
                    vaultCredentialsId: 'AnsibleVault',
                    extraVars: [
                        '@passwd.yml',
                        public_key_filepath: env.public_key_filepath,
                        user_to_add: env.user_to_add,
                        //ansible_become: [value: 'yes', hidden: true],
                        //ansible_become_method=sudo,
                        //ansible_user='{{ root_username }}',
                        //ansible_become_pass='{{ root_password }}'
                    ])
            }
        }
    }
}