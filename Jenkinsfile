pipeline {
    agent {
        kubernetes {
            label 'static-site-pod'
            defaultContainer 'kubectl'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bitnami/kubectl:1.29.1
    command:
    - cat
    tty: true
  - name: docker
    image: docker:24.0.5
    command:
    - cat
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
  """
        }
    }
    stages {
        stage('Check Pods') {
            steps {
                container('kubectl') {
                    sh 'kubectl get pods -A'
                }
            }
        }
    }
}
