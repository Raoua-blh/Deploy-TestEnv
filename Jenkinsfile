pipeline {
    agent any
    parameters {
        // string(name: 'AZURE_CLIENT_ID', description: 'Azure Client ID', defaultValue: '')
        // string(name: 'AZURE_SECRET', description: 'Azure Secret', defaultValue: '')
        // string(name: 'AZURE_TENANT', description: 'Azure Tenant ID', defaultValue: '')
        // string(name: 'RESOURCE_GROUP', description: 'Azure Resource Group', defaultValue: 'your-resource-group')
        // string(name: 'APP_NAME', description: 'Azure Web App Name', defaultValue: 'your-app-name')
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
                        mkdir -p downloaded-artifacts
                        cd downloaded-artifacts
                        curl -u ${nexus} ${nexusUrl} 
                        """
                    }
                }
            }
        }


        stage('Run Ansible Playbook') {
            steps {
                script{
                    // Authenticate with Azure CLI using service principal credentials
                    azureCLI(
                        principalCredentialId: 'Jenkins-'
                    )
               
                def jarfilePath = "${env.WORKSPACE}/downloaded-artifacts/${params.NEXUS_ARTIFACT.split('/').last()}"
                    sh """
                    ansible-playbook -i Inventory.yml AzureDeploy.yml  -e 'jarfile_path=${jarfilePath}'
                    """
                }
            }
        }
    }
}
