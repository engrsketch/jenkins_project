pipeline {
    agent {
        docker { image 'node:16-alpine' }
        }
    stages {
        stage('Git checkeout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'master', credentialsId: 'GIT_CREDENTIAL', url: 'https://github.com/engrsketch/jenkins_project.git'
            }
        }
        stage('build images'){
            steps{
                sh 'docker build -t engrsketch/multi-client:v1 ./client'
                sh 'docker build -t engrsketch/multi-server:v1./server'
                sh 'docker build -t engrsketch/multi-worker:v1 ./worker'
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