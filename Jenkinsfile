/* pipeline 변수 설정 */
def DOCKER_IMAGE_NAME = "syua0529/CloudComputing"
def NAMESPACE = "CloudComputing"
def VERSION = "${env.BUILD_NUMBER}"
def DATE = new Date();

podTemplate(label: 'builder',
            containers: [
                containerTemplate(name: 'gradle', image: 'gradle:7.6.1-jdk17', command: 'cat', ttyEnabled: true),
                containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
                containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.23.0', command: 'cat', ttyEnabled: true)
            ],
            volumes: [
                hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
                //hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker')
            ]) {
    node('builder') {
        // clone proejct
        stage('Checkout') {
            checkout scm
        }

        // test and build project using gradle
        stage('Gradle Build') {
            container('gradle') {
                sh "gradle -x test build"
            }
        }

        // build docker image and push it to docker hub
        stage('Docker build') {
            container('docker') {
                withCredentials([usernamePassword(
                    credentialsId: 'docker_hub_auth',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh 'echo $(USERNAME)'
                    sh 'echo $(PASSWORD)'
                    sh 'docker build -t ${DOCKER_IMAGE_NAME}:${VERSION} .'
                    sh 'docker login -u ${USERNAME} -p ${PASSWORD}'
                    sh 'docker push $(DOCKER_IMAGE_NAME):${VERSION}'
                }
            }
        }

        // deploy project to kubernetes
//         stage('Deploy') {
//             container('kubectl') {
//                 withCredentials([usernamePassword(
//                     credentialsId: 'docker_hub_auth',
//                     usernameVariable: 'USERNAME',
//                     passwordVariable: 'PASSWORD'
//                 ]) {
//                     sh "kubectl get ns ${NAMESPACE}|| kubectl create ns ${NAMESPACE}"
//                     sh "sed -i 's/변경전 내용/변경할 내용/g' ./k8s/k8s-deployment.yaml"
//                 }
//             }
//         }
    }
}