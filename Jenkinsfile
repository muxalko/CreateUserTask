#!groovy​

pipeline {
    agent any
    stages {
        stage('Upload_Public_Key') {
            steps {
                script {
                    // name for saving public key
                    env.public_key_filepath = env.WORKSPACE + "/public.key"
                    def userInput
                    try {
                    
                    timeout(time:5, unit:'MINUTES') {
                        userInput = input(
                                    id: 'userInput', message: 'New User Details',
                                    parameters: [
                                            string(defaultValue: 'bob',
                                                    description: 'This user will be created.',
                                                    name: 'username'),
                                            file(name: env.public_key_filepath, 
                                                description: 'Upload public.key file'
                                                ),
                                            choice( description:'How to run the setup ?', 
                                                    name:'ansible_using', 
                                                    choices: ['shell module','ansible plugin'].join('\n'),
                                                    )
                                    ],
                        )
                    }
                    } catch(err) { // input false
                        userInput = false
                        currentBuild.result = 'ABORTED'
                        error("The job was aborted")
                    }

                    if (userInput != false) {
                        env.user_to_add = userInput['username']?: 'bob'
                        echo("User to be created: ${env.user_to_add}")

                        validKey = sh(script: 'ssh-keygen -l -f ' + env.public_key_filepath, returnStdout: true)
                        env.validKey = validKey.contains('RSA')
                        echo("Valid key provided: ${env.validKey}")

                        env.ansibleMethod = userInput['ansible_using']
                    }
                }
            }
        }
        stage('Setup using Ansible Plugin') {
            when {
                expression { env.validKey && env.ansibleMethod == 'ansible plugin' }
            }
            steps {
                ansiblePlaybook(
                    playbook: 'setup.yml',
                    vaultCredentialsId: 'VaultPasswdFile',
                    extraVars: [
                        public_key_filepath: env.public_key_filepath,
                        user_to_add: env.user_to_add,
                    ])
            }
        }

        stage('Setup using Shell Module') {
            when {
                expression { env.validKey && env.ansibleMethod == 'shell module' }
            }
            steps {
                ansiblePlaybook(
                    playbook: 'setup_shell.yml',
                    vaultCredentialsId: 'VaultPasswdFile',
                    extraVars: [
                        public_key_filepath: env.public_key_filepath,
                        user_to_add: env.user_to_add,
                    ])
            }
        }

        stage("Gather_Facts") {
            steps {
                script {
                    gathered_info = sh( script: 'python3 whats_going_on', returnStdout: true)
                    echo (gathered_info)
                    archiveArtifacts 'data.json'
                }
            }
        }
    }
}
