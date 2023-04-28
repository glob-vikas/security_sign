pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                withCredentials([[
                    $class : 'AmazonWebServicesCredentialsBinding',
                    withCredentialsId : 'aws-jenkins-cli',
                    accessKeyVariable : AWS_ACCESS_KEY_ID,
                    secretKeyVariable : AWS_SECRET_ACCESS_KEY,]]){
                        sh "aws s3 ls"
                    }
            }
        }
    }
}
