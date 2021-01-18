pipeline {
  agent any
stages {
        stage("Prepare") {
            steps {
				echo "Notify the build status";
                //githubNotify description:"Started build" status: "PENDING"
            }
        }

		stage('Checkout') {
			steps {
				echo "Checking out from Git Repo";
                git credentialsId: 'GithubJenkins', url: 'https://github.com/USI-DevOps/demoaspnetwebapp.git', branch: 'master'
			}
		}
		
		stage('Restore packages'){
           steps{
              bat "dotnet restore demoaspnetwebapp\\demoaspnetwebapp.csproj"
           }
        }
        
        stage('Clean'){
            steps{
                bat "dotnet clean demoaspnetwebapp\\demoaspnetwebapp.csproj"
            }
        }
        
        stage('Build'){
           steps{
              bat "dotnet build demoaspnetwebapp\\demoaspnetwebapp.csproj --configuration Release /p:OutputPath=publish /p:ProductVersion=1.0.0.${env.BUILD_NUMBER}"
            }
        }
 
       stage('Publish'){
         steps{
           bat "dotnet publish demoaspnetwebapp\\demoaspnetwebapp.csproj"
         }
       }
       
       stage('Package') {
			steps {
				echo "Deploying to stage environment for more tests!";
                bat "del *.zip"
                zip zipFile: "demoaspnetwebapp-1.0.0.${env.BUILD_NUMBER}.zip", dir:"demoaspnetwebapp\\bin\\Debug\\netcoreapp3.1\\publish"
			}
		}

		stage('Deploy') {
			steps {
				echo "Deploying to stage environment for more tests!";
                unzip zipFile: "demoaspnetwebapp-1.0.0.${env.BUILD_NUMBER}.zip", dir:"C:\\inetpub\\wwwroot\\demoaspnetwebapp"
			}
		}
		
       stage('DeploytoAzure') {  
        steps {
            azureWebAppPublish appName: "DonetCoreApp",
                    azureCredentialsId: "AzureJenkins",
                    publishType: "file",
                    filePath: "**/*.*",
                    resourceGroup: "Learning",
                    sourceDirectory: "demoaspnetwebapp\\publish"
       }
     }
  }

}
