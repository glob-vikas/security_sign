pipeline {
    agent any
    stages {
            stage('Build') {
                steps {
                    script{
                        if (env.BRANCH_NAME == 'main'){
                            // echo currentBuild.changeSets
                            // sh "git diff --quiet --exit-code HEAD~1..HEAD upload_file.json"
                            files = sh "git diff-tree --no-commit-id --name-only -r ${env.GIT_COMMIT}"
                            // if (sh "git diff-tree --no-commit-id --name-only -r ${env.GIT_COMMIT}" == "upload_file.json"){
                                // "git diff --quiet upload_file.json"
                                echo "Hello ${files}"
                            // }
                        }
                    }
                    // withCredentials([[
                        // $class : 'AmazonWebServicesCredentialsBinding',
                        // credentialsId : 'aws-jenkins-cli',
                        // accessKeyVariable : 'AWS_ACCESS_KEY_ID',
                        // secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
                            ////sh "aws s3api create-bucket --bucket from-jenkins-9012 --region us-east-1"
                            // sh "aws events put-events --entries file://event.json --region ap-northeast-1"
                        // }
            }
        }
    }
}