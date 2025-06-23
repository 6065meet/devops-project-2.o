podTemplate(
    containers: [
        containerTemplate(name: 'docker', image: 'docker:24.0.5', ttyEnabled: true, command: 'cat',
            volumeMounts: [volumeMount(mountPath: '/var/run/docker.sock', name: 'docker-sock')]
        ),
        containerTemplate(name: 'kubectl', image: 'bitnami/kubectl:1.28.0-debian-11-r0', ttyEnabled: true, command: 'cat')
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
    ],
    serviceAccount: 'jenkins'
) {
    node(POD_LABEL) {
        stage('Deploy to Kubernetes') {
            container('kubectl') {
                sh 'kubectl apply -f deployment.yaml -n webpage'
                sh 'kubectl apply -f service.yaml -n webpage'
            }
        }
    }
}
