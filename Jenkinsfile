def upload_to_s3(updates_map) {
     withCredentials([[
            $class : 'AmazonWebServicesCredentialsBinding',
            credentialsId : 'aws-jenkins-cli',
            accessKeyVariable : 'AWS_ACCESS_KEY_ID',
            secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
            sh "curl -X POST -d ${updates_map} https://rkcn3zza99.execute-api.us-east-1.amazonaws.com/poc/create-poc"
            // def res = httpRequest(url: 'https://rkcn3zza99.execute-api.us-east-1.amazonaws.com/poc/create-poc', acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: {})
            // echo "${res}"
        }
//     for (int i = 0; i < files.size(); i++) {
//         withCredentials([[
//             $class : 'AmazonWebServicesCredentialsBinding',
//             credentialsId : 'aws-jenkins-cli',
//             accessKeyVariable : 'AWS_ACCESS_KEY_ID',
//             secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
//               //  sh "aws s3api create-bucket --bucket from-jenkins-9012 --region us-east-1"
//                 // sh "aws s3 cp upload_file.json s3://from-jenkins-9012 --region us-east-1"
//                 sh "aws s3 cp ${files[i]} s3://from-jenkins-9012 --region us-east-1"
//                 // sh "aws events put-events --entries file://event.json --region ap-northeast-1"
//                 echo "uploaded"
//             }
//     }
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
                            def files = sh (returnStdout: true, script: "git diff-tree --no-commit-id --name-status -r ${env.GIT_COMMIT}").split()
                            int index = 0
                            def updates_map = {"filesAdded": {}, "filesModified": {}}

                            while  (index < files.length){
                                if (files[index+1].endsWith("security_template.yaml") || files[index+1].endsWith("ignores.yaml")){
                                    def project_name = files[index+1].split('/')[0]
                                    
                                    if (files[index] == "A"){
                                        if (updates_map["filesAdded"][project_name]){
                                            updates_map["filesAdded"][project_name].add(files[index+1])
                                        }
                                        else{
                                            updates_map["filesAdded"][project_name] = [files[index+1]]
                                        }
                                    }
                                    else if(files[index] == "M"){
                                        if (updates_map["filesModified"][project_name]){
                                            updates_map["filesModified"][project_name].add(files[index+1])
                                        }
                                        else{
                                            updates_map["filesModified"][project_name] = [files[index+1]]
                                        }
                                    }
                                }
                                index = index + 2
                            }
                            // echo update_list
                            // echo "${files}"
                            echo "${updates_map}"
                            // upload_to_s3(updates_map)
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