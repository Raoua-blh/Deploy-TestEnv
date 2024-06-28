pipeline {
    agent any
    parameters {
        string(name: 'AZURE_CLIENT_ID', description: 'Azure Client ID', defaultValue: '')
        string(name: 'AZURE_SECRET', description: 'Azure Secret', defaultValue: '')
        string(name: 'AZURE_TENANT', description: 'Azure Tenant ID', defaultValue: '')
        string(name: 'RESOURCE_GROUP', description: 'Azure Resource Group', defaultValue: 'your-resource-group')
        string(name: 'APP_NAME', description: 'Azure Web App Name', defaultValue: 'your-app-name')
        string(name: 'NEXUS_URL', defaultValue: 'http://your-nexus-url/repository/maven-public/', description: 'URL of Nexus repository')
        string(name: 'NEXUS_ARTIFACT', defaultValue: 'com/example/front-office/9.9/front-office-9.9.jar', description: 'Path to Nexus artifact')    
    }
    stages {
        stage('Pull JAR from Nexus') {
            steps {
                script {
                    def nexusUrl = "${params.NEXUS_URL}/${params.NEXUS_ARTIFACT}"          
                    withCredentials([usernameColonPassword(credentialsId: 'Nexus', variable: 'nexus')]) {
                        sh """
                        cd downloaded-artifacts
                        curl -u ${nexus} ${nexusUrl} -o ${params.NEXUS_ARTIFACT} --create-dirs
                        """
                    }
                }
            }
        }


        stage('Run Ansible Playbook') {
            steps {
                script{
                def jarfilePath = "${env.WORKSPACE}/downloaded-artifacts/${params.NEXUS_ARTIFACT.split('/').last()}"

               //  sh '''
               // "ansible-playbook -i inventory AzureDeploy.yml -e 'azure_client_id=${params.azure_client_id}' -e 'azure_secret=${params.azure_secret}' -e 'azure_tenant=${params.azure_tenant}' -e 'resource_group=${params.resource_group}' -e 'app_name=${params.app_name}' -e 'jarfile_path=${params.NEXUS_ARTIFACT}'"               
               //  '''
                    sh """
                    ansible-playbook -i Inventory.yml AzureDeploy.yml \\
                        -e 'azure_client_id=${params.AZURE_CLIENT_ID}' \\
                        -e 'azure_secret=${params.AZURE_SECRET}' \\
                        -e 'azure_tenant=${params.AZURE_TENANT}' \\
                        -e 'resource_group=${params.RESOURCE_GROUP}' \\
                        -e 'app_name=${params.APP_NAME}' \\
                        -e 'jarfile_path=${jarfilePath}'
                    """
                }
            }
        }
    }
}
