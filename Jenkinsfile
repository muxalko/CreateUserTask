@Library('tools@main') _

env.public_key_filepath = 'public.key'
env.user_to_add = 'bob'

pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    // Variables for input
                    def inputConfig
                    def inputTest

                    // Get the input
                    def userInput = input(
                            id: 'userInput', message: 'Enter path of test reports:?',
                            parameters: [

                                    string(defaultValue: 'None',
                                            description: 'Path of config file',
                                            name: 'Config'),
                                    string(defaultValue: 'None',
                                            description: 'Test Info file',
                                            name: 'Test'),
                            ])

                    // Save to variables. Default to empty string if not found.
                    inputConfig = userInput.Config?:''
                    inputTest = userInput.Test?:''

                    // Echo to console
                    echo("IQA Sheet Path: ${inputConfig}")
                    echo("Test Info file path: ${inputTest}")

                    // Write to file
                    writeFile file: "inputData.txt", text: "Config=${inputConfig}\r\nTest=${inputTest}"

                    // Archive the file (or whatever you want to do with it)
                    archiveArtifacts 'inputData.txt'
                    
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
                    //colorized: true,
                    //disableHostKeyChecking: true,
                    vaultCredentialsId: 'VaultPasswdFile',
                    //inventoryPath: 'passwd.yml',
                    extraVars: [
                        public_key_filepath: env.public_key_filepath,
                        user_to_add: env.user_to_add,
                        //ansible_become: yes
                        //ansible_become_method=sudo,
                        //ansible_user='{{ root_username }}',
                        //ansible_become_pass='{{ root_password }}'
                    ])
            }
        }
    }
}