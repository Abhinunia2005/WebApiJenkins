pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'azure-service-principal'
        RESOURCE_GROUP = 'rg-jenkins8414'
        APP_SERVICE_NAME = 'jenkinsproject8414'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/Abhinunia2005/WebApiJenkins.git'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet restore'
                bat 'dotnet build --configuration Release'
                bat 'dotnet publish -c Release -o ./publish'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
                    bat "az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%"
                    bat "powershell Compress-Archive -Path ./publish/* -DestinationPath ./publish.zip -Force"
                    bat "az webapp deploy --resource-group %RESOURCE_GROUP% --name %APP_SERVICE_NAME% --src-path ./publish.zip --type zip"
                }
            }
        }

        stage('Restart Web App') {
            steps {
                bat "az webapp restart --name %APP_SERVICE_NAME% --resource-group %RESOURCE_GROUP%"
            }
        }

        stage('Verify Deployment') {
            steps {
                bat "az webapp log tail --name %APP_SERVICE_NAME% --resource-group %RESOURCE_GROUP%"
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful! You can visit your app at: http://jenkinsproject8414.azurewebsites.net'
        }
        failure {
            echo '❌ Deployment Failed! Check the logs above.'
        }
    }
}
