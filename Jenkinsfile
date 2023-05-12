def upload_to_s3(files) {
    for (int i = 0; i < files.size(); i++) {
        withCredentials([[
            $class : 'AmazonWebServicesCredentialsBinding',
            credentialsId : 'aws-jenkins-cli',
            accessKeyVariable : 'AWS_ACCESS_KEY_ID',
            secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
              //  sh "aws s3api create-bucket --bucket from-jenkins-9012 --region us-east-1"
                // sh "aws s3 cp upload_file.json s3://from-jenkins-9012 --region us-east-1"
                sh "aws s3 cp ${files[i]} s3://from-jenkins-9012 --region us-east-1"
                // sh "aws events put-events --entries file://event.json --region ap-northeast-1"
                echo "uploaded"
            }
    }
}


pipeline {
    agent any
    stages {
            stage('Build') {
                steps {
                    script{
                        if (env.BRANCH_NAME == 'main'){
                            // echo currentBuild.changeSets
                            // sh "git diff --quiet --exit-code HEAD~1..HEAD upload_file.json"
                            // def files = sh "git diff-tree --no-commit-id --name-only -r ${env.GIT_COMMIT}"
                            def files = sh (returnStdout: true, script: "git diff-tree --no-commit-id --name-only -r ${env.GIT_COMMIT}").split()
                            echo "status"
                            echo "${files}"
                            upload_to_s3(files)
                            echo "Ok"

                            // if (sh "git diff-tree --no-commit-id --name-only -r ${env.GIT_COMMIT}" == "upload_file.json"){
                                // path = "upload_file.json event.json"
                                // try{
                                    // sh "git diff --quiet --exit-code HEAD~1..HEAD ${path}"
                                // }
                                // catch (err){
                                    // withCredentials([[
                                        // $class : 'AmazonWebServicesCredentialsBinding',
                                        // credentialsId : 'aws-jenkins-cli',
                                        // accessKeyVariable : 'AWS_ACCESS_KEY_ID',
                                        // secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
                                          ////  sh "aws s3api create-bucket --bucket from-jenkins-9012 --region us-east-1"
                                            // echo "uploaded"
                                            // sh "aws s3 cp upload_file.json s3://from-jenkins-9012 --region us-east-1"
                                            // sh "aws s3 cp event.json s3://from-jenkins-9012 --region us-east-1"
                                            //sh "aws events put-events --entries file://event.json --region ap-northeast-1"
                                        // }
                                // }
                            // }
                        }
                    }
            }
        }
    }
}