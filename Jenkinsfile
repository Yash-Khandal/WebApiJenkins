pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'jenkins-azure-sp'
        RESOURCE_GROUP = 'terraform-rg-dotnet'          // Must match Terraform
        APP_SERVICE_NAME = 'terraform-jenkins'
    }
    stages {
        // Stage 1: Checkout Code
        stage('Checkout') {
            steps {
                git branch: 'master', 
                url: 'https://github.com/Yash-Khandal/WebApiJenkins.git'
            }
        }

        // Stage 2: Build .NET App
        stage('Build') {
            steps {
                bat 'dotnet restore'
                bat 'dotnet build --configuration Release'
                bat 'dotnet publish -c Release -o ./publish'
            }
        }

        // Stage 3: Deploy to Azure
        stage('Deploy to Azure') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'AZURE_CREDENTIALS',
                        usernameVariable: 'AZURE_CLIENT_ID',
                        passwordVariable: 'AZURE_CLIENT_SECRET'
                    ),
                    string(
                        credentialsId: 'AZURE_SUBSCRIPTION_ID',
                        variable: 'AZURE_SUBSCRIPTION_ID'
                    ),
                    string(
                        credentialsId: 'AZURE_TENANT_ID',
                        variable: 'AZURE_TENANT_ID'
                    )
                ]) {
                    bat '''
                        az login --service-principal \
                          -u %AZURE_CLIENT_ID% \
                          -p %AZURE_CLIENT_SECRET% \
                          --tenant %AZURE_TENANT_ID%
                        
                        az account set --subscription %AZURE_SUBSCRIPTION_ID%
                        
                        az webapp deploy \
                          --resource-group %RESOURCE_GROUP% \
                          --name %APP_SERVICE_NAME% \
                          --src-path ./publish \
                          --type zip
                    '''
                }
            }
        }
    }
}
