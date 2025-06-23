pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent'
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:24.0.5
    command:
    - cat
    tty: true
    volumeMounts:
    - name: docker-sock
      mountPath: /var/run/docker.sock
  - name: kubectl
    image: bitnami/kubectl:latest
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

    environment {
        DOCKER_IMAGE = "6065meet/static-website"
        TAG = "latest"
    }

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                container('docker') {
                    sh 'docker build -t $DOCKER_IMAGE:$TAG .'
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
                    }
                }
            }
        }

        stage('Push Image') {
            steps {
                container('docker') {
                    sh 'docker push $DOCKER_IMAGE:$TAG'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                container('kubectl') {
                    sh '''
                    kubectl set image deployment/static-website static-website=$DOCKER_IMAGE:$TAG -n portfolio || \
                    kubectl create deployment static-website --image=$DOCKER_IMAGE:$TAG -n portfolio
                    kubectl expose deployment static-website --type=NodePort --port=80 -n portfolio || true
                    '''
                }
            }
        }
    }
}
