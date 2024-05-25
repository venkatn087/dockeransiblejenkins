pipeline {
    agent any
    tools {
        maven 'maven_3.9.6'
    }
    environment {
    DOCKER_TAG = getVersion()
    }
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github', 
                      url: 'https://github.com/venkatn087/dockeransiblejenkins.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Docker image') {
            steps {
              sh 'docker build -t venkatn087/webapp:${DOCKER_TAG} .'  
            }
        }
         stage('Push Docker image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u venkatn087 -p ${dockerhubpwd}'
                 }
               sh 'docker push venkatn087/webapp:${DOCKER_TAG}'  
            }
        }
        stage('Deploy the Docker image') {
            steps {
                ansiblePlaybook credentialsId: 'ansible_dev_server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
        
    }
}

def getVersion() {
    def commitHash = sh label: 'commit_id', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
