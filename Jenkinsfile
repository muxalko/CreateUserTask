
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
                    def inputGetFile(String savedfile = null) {
                        def filedata = null
                        def filename = null
                        
                        if (savedfile == null) {
                            def inputFile = input message: 'Upload file', parameters: [file(name: 'file_upload'), string(name: 'filename', defaultValue: 'public.key')]
                            filedata = inputFile['library_data_upload']
                            filename = inputFile['filename']
                        } else {
                            def inputFile = input message: 'Upload file', parameters: [file(name: 'file_upload')]
                            filedata = inputFile
                            filename = savedfile
                        }

                        // Read contents and write to workspace
                        writeFile(file: filename, encoding: 'Base64', text: filedata.read().getBytes().encodeBase64().toString())

                        filedata.delete()
                        return filename
                    }
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
