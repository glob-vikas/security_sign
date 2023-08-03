import groovy.json.JsonOutput


def upload_to_s3(updates_map) {
     withCredentials([[
            $class : 'AmazonWebServicesCredentialsBinding',
            credentialsId : 'aws-jenkins-cli',
            accessKeyVariable : 'AWS_ACCESS_KEY_ID',
            secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
                if (updates_map["files_added"] || updates_map["files_modified"]){
                    def create_json = JsonOutput.toJson(updates_map)
                    def create = create_json.toString()
                    sh "curl -H 'Accept: application/json' -X POST --data '${create}' https://rkcn3zza99.execute-api.us-east-1.amazonaws.com/poc/create-poc"
                    echo "Invoked Cread Project Lambda"
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
                            def files = sh (returnStdout: true, script: "git log -m -1 --name-status --pretty=format: ${env.GIT_COMMIT}").split()

                            echo "${files}"

                            def user_info = sh (returnStdout: true, script: "git remote get-url origin").split("/")

                            def repo_name = user_info[-1].split("\\.")[0]

                            int index = 0
                            def updates_map = ["files_added": [:], "files_modified": [:]]
                            
                            updates_map["repo_owner"] = user_info[-2]
                            updates_map["repo_name"] = user_info[-1].split("\\.")[0]
                            
                            while  (index < files.length){
                                if (files[index+1].endsWith("configuration.yaml") || files[index+1].endsWith("ignore.yaml") || files[index+1].endsWith("ruleset.yaml")){
                                    def project_name = files[index+1].split('/')[0]
                                    
                                    if (files[index] == "A"){
                                        if (updates_map["files_added"][project_name]){
                                            updates_map["files_added"][project_name].add(files[index+1])
                                        }
                                        else{
                                            updates_map["files_added"][project_name] = [files[index+1]]
                                        }
                                    }
                                    else if(files[index] == "M"){
                                        if (updates_map["files_modified"][project_name]){
                                            updates_map["files_modified"][project_name].add(files[index+1])
                                        }
                                        else{
                                            updates_map["files_modified"][project_name] = [files[index+1]]
                                        }
                                    }
                                }
                                index = index + 2
                            }
                            echo "${updates_map}"
                            upload_to_s3(updates_map)
                        }
                    }
            }
        }
    }
}