podTemplate(
    containers: [
        containerTemplate(
            name: 'docker',
            image: 'docker:24.0.5',
            command: 'cat',
            ttyEnabled: true,
            volumeMounts: [
                // Correct usage here
                volumeMount(mountPath: '/var/run/docker.sock', name: 'docker-sock')
            ]
        ),
        containerTemplate(
            name: 'kubectl',
            image: 'bitnami/kubectl:1.28.0-debian-11-r0',
            command: 'cat',
            ttyEnabled: true
        )
    ],
    volumes: [
        hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
    ]
) {
    node(POD_LABEL) {
        stage('Checkout') {
            checkout scm
        }

        stage('Build Docker Image') {
            container('docker') {
                sh 'docker build -t 6065meet/static-website:latest .'
            }
        }

        stage('Login to DockerHub') {
            container('docker') {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            container('docker') {
                sh 'docker push 6065meet/static-website:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            container('kubectl') {
                sh 'kubectl apply -f deployment.yaml -n webpage'
                sh 'kubectl apply -f service.yaml -n webpage'
            }
        }
    }
}
