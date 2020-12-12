//@Library('tools@main') _

pipeline {
    agent any
    stages {
        stage('Upload_Public_Key') {
            steps {
                script {
                    def username = input(
                            id: 'userInput', message: 'Username to create',
                            parameters: [
                                    string(defaultValue: 'bob',
                                            description: 'This user will be created.',
                                            name: 'username'),
                            ])

                    def inputFile = input 
                          message: 'Upload file', 
                          parameters: [file(name: 'public.key', description: 'Upload only public.key file')]        
                    
                    def fileContent = readFile "${inputFile}"
                        
                    echo ("INPUT FILE PATH IS : ${inputFile1}")
                    echo("FILE CONTENT IS: ${fileContent}") 

                    env.user_to_add = username ?: 'bob'
                    echo("User to be created: ${env.user_to_add}")

                    // name for saving public key
                    env.public_key_filepath = "public-key.${env.user_to_add}"

                    // import the actual public key from user input
                    //def inputFile = uploadFile.inputGetFile(env.public_key_filepath)

                    validKey = sh(script: 'ssh-keygen -l -f ' + env.public_key_filepath, returnStdout: true)

                    echo("Valid key provided: ${validKey.contains('RSA')}")
                }
            }
        }
        stage('Setup') {
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
