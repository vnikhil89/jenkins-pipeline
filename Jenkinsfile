
pipeline {

  environment {
    dockerimagename = "vniks/python-hello-app"
    dockerImage = ""
  }
  agent any
  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/vnikhil89/python-flask.git'
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          sh 'podman build -t docker.io/vniks/python-hello-app:v2 .'
                }
            }
        }
    stage('Pushing Image') {
      steps{
        script {
          withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]){
          sh 'podman login docker.io -u vniks -p ${dockerhubpwd}'
          }
          sh 'podman push docker.io/vniks/python-hello-app:v2'
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        sshagent(['k8s']) {
           sh "scp -o StrictHostKeyChecking=no hello.yaml hello-svc.yaml root@10.0.200.133:/root"
        script {
           sh "ssh root@10.0.200.133 kubectl create -f hello.yaml"
           sh "ssh root@10.0.200.133 kubectl create -f hello-svc.yaml"
           }
        }

      }

    }
  }
}
    
