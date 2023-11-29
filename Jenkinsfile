pipeline {
    agent any
    environment {
        dockerImage = ''
        registry = 'ashay1987/flight_price_predictor'
        registryCredential = 'dockerhub'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/gawaliashay/flight-price-predictor.git']])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Upload Image To Dockerhub') {
            steps {
                script{
                    docker.withRegistry('', registryCredential) {
                    dockerImage.push()
                    }
                }
            }
        }
        // Stopping Docker containers for cleaner Docker run
        stage('stop previous container if any') {
            steps {
                sh 'docker ps -f name=flight_Container -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -fname=flight_Container -q | xargs -r docker container rm'
                }
            }
        // Running Docker container, make sure port 3000 is opened
        stage('Run Docker Container') {
            steps{
                script{
                    dockerImage.run("-p 3000:3000 --rm --name flight_Container")
                }
            }
        }
        
    }
}

