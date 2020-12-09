@Library('tools@master') _

pipeline {
    agent any
    stages {
        stage("Upload_Public_Key") {
            steps {
                script {
                    def inputFile = uploadFile.inputGetFile('public.key')
                    sh "ls -ltR"
                }
            }        
        }
    }
}
