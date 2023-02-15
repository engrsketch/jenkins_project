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
                // sh 'ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 sh /home/jenkins/jenkins_project/remove_dir.sh'
                sh '''
                ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 sh /home/jenkins/jenkins_project/remove_dir.sh
                scp -o StrictHostKeyChecking=no -r ./client jenkins@192.168.1.183:/home/jenkins/jenkins_project
                scp -o StrictHostKeyChecking=no -r ./worker jenkins@192.168.1.183:/home/jenkins/jenkins_project
                scp -o StrictHostKeyChecking=no -r ./server jenkins@192.168.1.183:/home/jenkins/jenkins_project
                '''
            }
            sshagent (credentials: ['ifeanyi']) {
                sh '''
                    ssh -o StrictHostKeyChecking=no ifeanyi@192.168.1.183 sh /home/ifeanyi/jenkins_project/remove_dir.sh
                    scp -o StrictHostKeyChecking=no -r ./k8s ifeanyi@192.168.1.195:/home/ifeanyi/jenkins_project/
                    '''
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
                sshagent (credentials: ['jenkins']) {
                withCredentials([string(credentialsId: 'DOCKER_CREDENTIALS', variable: 'docker_passwd')]){
                sh 'ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker docker login -u dalusianyi@gmail.com -p ${docker_passwd}'
                sh 'ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker docker push engrsketch/multi-client:v1'
                sh 'ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker docker push engrsketch/multi-server:v1'
                sh 'ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker docker push  engrsketch/multi-worker:v1'
                }
                sh 'ssh -o StrictHostKeyChecking=no jenkins@192.168.1.183 docker image rm -f engrsketch/multi-client:v1 engrsketch/multi-server:v1 engrsketch/multi-worker:v1'
                }
            
        }
        }
        }
        }
