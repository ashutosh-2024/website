pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:ashutosh'
       appRegistry = "202051215_capstone"
       capstoneRegistry = "484030279965.dkr.ecr.ap-south-1.amazonaws.com/202051215_capstone"
       cluster = "ashutosh"
        service = "202051215"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/ashutosh-2024/website'
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
            withAWS(credentials: '<CredentialID>', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
