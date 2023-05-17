/* pipeline 변수 설정 */
def DOCKER_IMAGE_NAME = "twofootdog/project-repo"           // 생성하는 Docker image 이름
def DOCKER_IMAGE_TAGS = "batch-visualizer-auth"  // 생성하는 Docker image 태그
def NAMESPACE = "ns-project"
def VERSION = "${env.BUILD_NUMBER}"
def DATE = new Date();

podTemplate(label: 'builder',
            containers: [
                containerTemplate(name: 'gradle', image: 'gradle:7.6.1-jdk17-alpine', command: 'cat', ttyEnabled: true),
                containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
                containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.23.0', command: 'cat', ttyEnabled: true)
            ],
            volumes: [
                hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
                //hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker')
            ]) {
    node('builder') {
        stage('Checkout') {
            checkout scm
        }

        stage('Gradle Build') {
            container('gradle') {
                sh "gradle clean build"
            }
        }
    }
}