pipeline {
    agent any
    parameters {
    
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
                    
                    echo "Workspace: ${env.WORKSPACE}"
                    echo "Nexus Artifact: ${params.NEXUS_ARTIFACT}"
                    def jarfilePath = "${env.WORKSPACE}/downloaded-artifacts/${params.NEXUS_ARTIFACT}"
                    echo "Jarfile Path: ${jarfilePath}
                    // Authenticate with Azure CLI using service principal credentials
                    azureCLI(
                        principalCredentialId: 'Jenkins-'
                    )
               
                    sh """
                    ansible-playbook -i Inventory.yml AzureDeploy.yml  -e 'jarfile_path=${jarfilePath}'
                    """
                }
            }
        }
    }
}
