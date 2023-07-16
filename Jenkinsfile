import groovy.json.JsonOutput


def upload_to_s3(updates_map) {
     withCredentials([[
            $class : 'AmazonWebServicesCredentialsBinding',
            credentialsId : 'aws-jenkins-cli',
            accessKeyVariable : 'AWS_ACCESS_KEY_ID',
            secretKeyVariable : 'AWS_SECRET_ACCESS_KEY',]]){
                if (updates_map["filesAdded"].size()>0){
                    def create_json = JsonOutput.toJson(updates_map["filesAdded"])
                    def create = create_json.toString()
                    sh "curl -H 'Accept: application/json' -X POST --data '${create}' https://rkcn3zza99.execute-api.us-east-1.amazonaws.com/poc/create-poc"
                    echo "Invoked Cread Project Lambda"
                }
                if (updates_map["filesModified"].size()>0){
                    def updates_json = JsonOutput.toJson(updates_map["filesModified"])
                    def updates = updates_json.toString()
                    sh "curl -H 'Accept: application/json' -X POST --data '${updates}' https://mc7tyk45r1.execute-api.us-east-1.amazonaws.com/poc-up/update-files-poc"
                    echo "Invoked Update files Lambda"
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
                            def files = sh (returnStdout: true, script: "git diff-tree --no-commit-id --name-status -r HEAD").split()
                            // def files = sh (returnStdout: true, script: "git diff-tree --no-commit-id --name-status -r ${env.GIT_COMMIT}").split()
                            echo "${files}"

                            sh "git log -m --name-only"
                            int index = 0
                            def updates_map = ["filesAdded": [:], "filesModified": [:]]
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
                            echo "${updates_map}"
                            // upload_to_s3(updates_map)
                        }
                    }
            }
        }
    }
}