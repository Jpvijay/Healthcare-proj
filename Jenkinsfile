pipeline {
  agent any
     tools {
       maven 'M2_HOME'
           }
     
  stages {
    stage('Git Checkout') {
      steps {
        echo 'This stage is to clone the repo from github'
        git branch: 'master', url: 'https://github.com/Jpvijay/Healthcare-proj.git'
                        }
            }
    stage('Create Package') {
      steps {
        echo 'This stage will compile, test, package my application'
        sh 'mvn package'
                          }
            }
    stage('Test Results') {
      steps {
        echo 'This is for generating Test Results'
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: '/var/lib/jenkins/workspace/Insureme/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
          }
     stage('Create Docker Image') {
      steps {
        echo 'This stage will Create a Docker image'
        sh 'docker build -t vijayhub11/medisure:latest .'
                          }
            }
    stage('Login to Dockerhub') {
      steps {
        echo 'This stage will loginto Dockerhub' 
        withCredentials([usernamePassword(credentialsId: 'dockercred', passwordVariable: 'dockpass', usernameVariable: 'dockuser')]) {
        sh 'docker login -u ${dockusername} -p ${dockpass}'
            }
         }
     }
    stage('Docker Push-Image') {
      steps {
        echo 'This stage will push my new image to the dockerhub'
        sh 'docker push vijayhub11/medisure:latest'
            }
      }
  }
}
 
