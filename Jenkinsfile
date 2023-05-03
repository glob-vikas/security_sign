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
                        // sh "aws s3api create-bucket --bucket from-jenkins-9012 --region us-east-1"
                        sh "aws events put-events --entries file://event.json --region ap-northeast-1"
                    }
            }
        }
    }
}