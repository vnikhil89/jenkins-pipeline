pipeline {
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
    }
  }
    stage('Build Docker Image') {
      steps {
        container('docker') {
          withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]){
            sh 'docker login docker.io -u vniks -p ${dockerhubpwd}'
            sh "docker build -t vniks/promo-app:dev ."  // when we run docker in this step, we're running it via a shell on the docker build-pod container, 
            sh "docker push vniks/promo-app:dev"        // which is just connecting to the host docker deaemon
        }
        }   
      }
    }
  }
