pipeline {
    agent any
    environment {
        RESOURCE_GROUP = 'terraform-rg-dotnet'
        APP_SERVICE_NAME = 'terraform-jenkins'
    }
    
    stages {
        // Stage 1: Checkout Code
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'master']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Yash-Khandal/WebApiJenkins.git'
                    ]]
                ])
            }
        }

        // Stage 2: Build .NET App
        stage('Build') {
            steps {
                bat 'dotnet restore WebApiJenkins.sln'
                bat 'dotnet build WebApiJenkins.sln --configuration Release'
                bat 'dotnet publish WebApiJenkins.sln -c Release -o ./publish'
            }
        }

        // Stage 3: Deploy to Azure
        stage('Deploy to Azure') {
            steps {
                withCredentials([
                    string(credentialsId: '341c0480-6cf6-4f3f-b85a-59ac32084293', variable: 'AZURE_CLIENT_ID'),
                    string(credentialsId: ' q4r8Q~xzxT-ae7Gx74t3MilSIumZvQg_TO1wQc-0', variable: 'AZURE_CLIENT_SECRET'),
                    string(credentialsId: '341f4047-ffad-4c4a-a0e7-b86c7963832b', variable: 'AZURE_TENANT_ID'),
                    string(credentialsId: '6c1e198f-37fe-4942-b348-c597e7bef44b', variable: 'AZURE_SUBSCRIPTION_ID')
                ]) {
                    bat '''
                        echo "Logging in to Azure..."
                        az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%
                        
                        echo "Setting subscription..."
                        az account set --subscription %AZURE_SUBSCRIPTION_ID%
                        
                        echo "Creating zip package..."
                        cd publish
                        zip -r ../webapp.zip .
                        cd ..
                        
                        echo "Deploying to Azure App Service..."
                        az webapp deployment source config-zip \
                          --resource-group %RESOURCE_GROUP% \
                          --name %APP_SERVICE_NAME% \
                          --src ./webapp.zip
                        
                        echo "Deployment complete!"
                    '''
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
