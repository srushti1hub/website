pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:62ec39b0-b26d-432c-a079-449c924e011e'
       appRegistry = "059052936207.dkr.ecr.ap-northeast-1.amazonaws.com/capstone-project"
       capstoneRegistry = "https://059052936207.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "CapstoneProject"
       service = "CapstoneProjectService"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/srushti1hub/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '62ec39b0-b26d-432c-a079-449c924e011e', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
