pipeline {
    agent {
        docker { image 'docker:23.0.1-cli' }
        }


    stages {
        stage('Git checkeout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'master', credentialsId: 'GIT_CREDENTIAL', url: 'https://github.com/engrsketch/jenkins_project.git'
            }
        }
        statge(build images){
            steps{
                sh 'docker build -t engrsketch/multi-client:v1 ./client'
                sh 'docker build -t engrsketch/multi-server:v1./server'
                sh 'docker build -t engrsketch/multi-worker:v1 ./worker'
            }
        }
        statge(push images){
            withDockerRegistry(credentialsId: 'docker_details', url: 'hub.docker.com') {
                steps{
                sh 'docker push engrsketch/multi-client:v1'
                sh 'docker push engrsketch/multi-server:v1'
                sh 'docker push  engrsketch/multi-worker:v1'
            }
        }

        }
        }
    }