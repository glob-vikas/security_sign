import groovy.json.JsonOutput
import groovy.json.JsonSlurper


def upload_to_s3(updates_map, aws_api) {
    // Method to call api to invoke new_project lambda with map as payload.

     withCredentials([string(credentialsId: 'aws-s3-api-key', variable: 'TOKEN')]){
        // Using api key from jenkins credentials

            def updates_json = JsonOutput.toJson(updates_map)
            // converted map into json

            def updates = updates_json.toString()
            // converted json into string, so that it can be send as payload.

            def response = sh (returnStdout: true, script: "curl -H 'Accept: application/json' -H 'x-api-key: ${TOKEN}'  -X POST --data '${updates}' '${aws_api}'")

            def res = new JsonSlurper().parseText(response)
            // converted response into valid json and print message as per the response.
            
            if (res["status"] == "Fail"){
                echo "Failed in API"
                error(res["message"])
            }else{
                echo "${res['message']}"
            } 
        }
}

def common_ops(aws_api){
   def files = sh (returnStdout: true, script: "git log -m -1 --name-status --pretty=format: ${env.GIT_COMMIT}").split()
        // Command to track all the changed files in the current commit and to store that as a list with their Mode
        // Ex: M for Modified, A for Added etc

        def repo_info = sh (returnStdout: true, script: "git remote get-url origin").split("/")
        // Command to fetch the repository details like repo name and repo owner.

        def repo_name = repo_info[-1].split("\\.")[0]

        int index = 0
        def updates_map = ["files_added": [:], "files_modified": [:]]

        // Initialized a empty map with keys:
        // 1. files_added (key to hold the records of new components are created in the commit along with the added files).
        // 2. files_modified (key to hold the records of components in which there is a change in any of its file(s)).
        // 3. organization_name : contains name of organization (seceng). define below
        // 4. repo_name : contains the name of repository. define below

        updates_map["organization_name"] = repo_info[-2]

        updates_map["repo_name"] = repo_info[-1].split("\\.")[0]

        updates_map["branch_name"] = env.BRANCH_NAME


        // Loop to iterate all the files tracked in files variable and check if any of the files endswith configuration.yaml or ignore.yaml ruleset.yaml
        // This condition only be passed if any of the below files is present in files variable:
        // 1. configuration.yaml
        // 2. ignore.yaml
        // 3. global_ignore.yaml
        // 4. ruleset.yaml

        // If confition is satified for any file, it will check for its Mode:
        // if mode is A - the file will be added as a list in the specific component key.
        // if mode is M - the file will be added as a list in the specific component key.

        // Note: mode of every file is present at next index to it, that's why we are iterating every second next variable to get the file name.

        while  (index < files.length){
            if (files[index+1].endsWith("configuration.yaml") || files[index+1].endsWith("ignore.yaml") || files[index+1].endsWith("ruleset.yaml")){
                def component_name = files[index+1].split('/')[0]

                if (files[index] == "A"){
                    if (updates_map["files_added"][component_name]){                                 // If a key with component_name is already present, add the files in the same list
                        updates_map["files_added"][component_name].add(files[index+1])
                    }
                    else{                                                                           // else create a new key with component name and add files into it as a list.
                        updates_map["files_added"][component_name] = [files[index+1]]
                    }
                }
                else if(files[index] == "M"){
                    if (updates_map["files_modified"][component_name]){
                        updates_map["files_modified"][component_name].add(files[index+1])
                    }
                    else{
                        updates_map["files_modified"][component_name] = [files[index+1]]
                    }
                }
            }
            index = index + 2
        }
        echo "${updates_map}"

        // Checked if there is any file inside files_added key or files_modified key. if yes, call upload_to_s3()

        if (updates_map["files_added"] || updates_map["files_modified"]){
            upload_to_s3(updates_map, aws_api)
        }
}

pipeline {
    agent any
    stages {
            stage('Update S3') {
                steps {
                    script{
                            if (env.BRANCH_NAME == 'develop'){
                                aws_api = "https://wo8pv3sbi5.execute-api.us-east-1.amazonaws.com/dev/new_project"
                                common_ops(aws_api)
                            }
                            else if(env.BRANCH_NAME == 'staging'){
                                aws_api = "https://f9td53xu89.execute-api.us-east-1.amazonaws.com/dev/new_project"
                                common_ops(aws_api)
                            }
                        }
                    }
            }
        }
    }
