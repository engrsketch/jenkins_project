pipeline {
    agent any
    stages {
        stage('Git checkeout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'master', credentialsId: 'GIT_CREDENTIAL', url: 'https://github.com/engrsketch/jenkins_project.git'
            }
        }
        stage('Copy to docker and kubeapi server'){
            steps{
                sshagent (credentials: ['jenkins']) {
                sh 'scp -o StrictHostKeyChecking=no -r ./client jenkins@192.168.1.183:/home/jenkins/jenkins_project\
                    && scp -o StrictHostKeyChecking=no -r ./worker jenkins@192.168.1.183:/home/jenkins/jenkins_project && \
                    scp -o StrictHostKeyChecking=no -r ./server jenkins@192.168.1.183:/home/jenkins/jenkins_project'
            }
            sshagent (credentials: ['ifeanyi']) {
                sh 'scp -o StrictHostKeyChecking=no -r ./k8s ifeanyi@192.168.1.195:/home/ifeanyi/jenkins_project'
            }
            }
        }
        stage('build images'){
            steps{
                sshagent (credentials: ['jenkins']) {
                sh '''
                ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker build -t engrsketch/multi-client:v1 /home/jenkins/jenkins_project/client
                ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker build -t engrsketch/multi-server:v1 /home/jenkins/jenkins_project/server
                ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker build -t engrsketch/multi-worker:v1 /home/jenkins/jenkins_project/worker
                '''
            }
            }
        }
        stage('push images'){
            steps{
                withDockerRegistry(credentialsId: 'docker_details', url: 'hub.docker.com') 
                sh 'docker push engrsketch/multi-client:v1'
                sh 'docker push engrsketch/multi-server:v1'
                sh 'docker push  engrsketch/multi-worker:v1'
            
        }
        }
        }
        }
