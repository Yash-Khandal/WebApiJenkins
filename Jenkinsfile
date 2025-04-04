pipeline {
    agent any
    environment {
        AZURE_SUBSCRIPTION_ID = credentials('6c1e198f-37fe-4942-b348-c597e7bef44b')
        AZURE_TENANT_ID       = credentials('341f4047-ffad-4c4a-a0e7-b86c7963832b')
        AZURE_CREDENTIALS     = credentials('0250993e-2ede-47dd-9be9-222958a77579')
        
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Yash-Khandal/azure-app-service.git'
            }
        }
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform Plan') {
            steps {
                sh '''
                    terraform plan \
                    -var "azure_subscription_id=${AZURE_SUBSCRIPTION_ID}" \
                    -var "azure_tenant_id=${AZURE_TENANT_ID}" \
                    -var "azure_client_id=${AZURE_CREDENTIALS_USR}" \
                    -var "azure_client_secret=${AZURE_CREDENTIALS_PSW}"
                '''
            }
        }
        stage('Terraform Apply') {
            steps {
                input message: 'Approve deployment?', ok: 'Yes'
                sh '''
                    terraform apply -auto-approve \
                    -var "azure_subscription_id=${AZURE_SUBSCRIPTION_ID}" \
                    -var "azure_tenant_id=${AZURE_TENANT_ID}" \
                    -var "azure_client_id=${AZURE_CREDENTIALS_USR}" \
                    -var "azure_client_secret=${AZURE_CREDENTIALS_PSW}"
                '''
            }
        }
    }
}
