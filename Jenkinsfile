podTemplate(containers: [
    containerTemplate(name: 'podman', image: 'vniks/podman-agent:v1', command: 'cat', privileged: true, user: 'root', ttyEnabled: true),
    containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:latest', command: 'cat', ttyEnabled: true)
  ],
  volumes: [
  hostPathVolume(mountPath: '/run/podman/podman.sock', hostPath: '/run/podman/podman.sock')
]) {

    node(POD_LABEL) {
        stage('Get a git project') {
            git 'https://github.com/vnikhil89/jenkins-pipeline.git'
            container('podman') {
                stage('Build a docker image and push') {
                  withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]){
                    sh 'podman login docker.io -u vniks -p ${dockerhubpwd}'
                    sh 'podman build -t docker.io/vniks/jenkins-pipeline:v1 .'
                    sh 'podman push docker.io/vniks/jenkins-pipeline:v1'
                  }
                }
            }
        }

    }
    node(POD_LABEL) {
        stage('Deployment') {
            git 'https://github.com/vnikhil89/jenkins-pipeline.git'
            container('kubectl') {
                stage('Build containers') {
                  sh 'kubectl replace --force -f hello.yaml'
                  sh 'kubectl replace --force -f hello-svc.yaml'
                }
            }
        }

    }    
}
