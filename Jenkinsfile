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
     stage('Create Docker Image') {
      steps {
        echo 'This stage will Create a Docker image'
        sh 'docker build -t vijayhub11/healthcare:latest .'
                          }
            }
    stage('Login to Dockerhub') {
      steps {
        echo 'This stage will loginto Dockerhub' 
        withCredentials([usernamePassword(credentialsId: 'dockercred', passwordVariable: 'dockpass', usernameVariable: 'dockusername')]) {
        sh 'docker login -u ${dockusername} -p ${dockpass}'
            }
         }
     }
    stage('Docker Push-Image') {
      steps {
        echo 'This stage will push my new image to the dockerhub'
        sh 'docker push vijayhub11/healthcare:latest'
            }
      }
    stage('Terraform Operations for Production workspace') {
    steps {
        script {
            def terraformHome = '/usr/bin/terraform'

            withAWS(credentialsId: 'awskeys', region: "${env.AWS_REGION}") {
                sh """
                #!/bin/bash
                set -e
                export PATH=$PATH:${terraformHome}
                terraform workspace select prod || terraform workspace new prod
                terraform init
                terraform plan
                terraform destroy -auto-approve
                """
            }
        }
    }
}

stage('Terraform destroy & apply for production workspace') {
    steps {
        script {
            def terraformHome = '/usr/bin/terraform'

            withAWS(credentialsId: 'awskeys', region: "${env.AWS_REGION}") {
                sh """
                #!/bin/bash
                set -e
                export PATH=$PATH:${terraformHome}
                terraform apply -auto-approve
                """
            }
        }
    }
}
    stage('get kubeconfig for production') {
      steps {
        sh 'aws eks update-kubeconfig --region us-east-1 --name prod-cluster'
        sh 'kubectl get nodes'
      }
    }
    stage('Deploying the application to production') {
      steps {
        sh 'kubectl apply -f app-deploy.yml'
        sh 'kubectl get svc'
      }
    }
  }
}
 
