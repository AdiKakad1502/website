pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-west-2:202052302_keystone'
       appRegistry = "267853679092.dkr.ecr.us-west-2.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://267853679092.dkr.ecr.us-west-2.amazonaws.com"
       cluster = "CapstoneProjectCluster"
        service = "capstone_service_devops"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/AdiKakad1502/website'
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
            withAWS(credentials: '202052302_keystone', region: 'us-west-2'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
