pipeline{
    agent any
    environment {
        DOCKER_TAG = getVersion()
    }

    
    stages{
        stage('SCM'){
            steps{
                git branch: 'main', credentialsId: 'github',
                url: 'https://github.com/akhileshtrivedi/integrat-jenkins-docker-ansible'
            }
        }
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        stage('Docker Build'){
            steps{
                sh "docker build . -t akhileshaca/webapp:${DOCKER_TAG}"
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]){
                sh "docker login -u akhileshaca -p ${dockerHubPwd}"
                }
                
                sh "docker push akhileshaca/webapp:${DOCKER_TAG}"
            }
        }
        stage('Docker Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'playbook.yml'
            }
        }
    }
}


def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
