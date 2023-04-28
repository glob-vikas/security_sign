pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                withCredentials([[
                    $class : 'AmazonWebServicesCredentialsBinding',
                    credentialsId : 'aws-jenkins-cli',
                    accessKeyVariable : 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
                        sh "/usr/local/bin/aws s3 ls"
                    }
            }
        }
    }
}
