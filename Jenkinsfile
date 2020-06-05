pipeline {
    environment {
        registry = 'hegistva/jenkins-docker-test'
        DOCKER_PWD = credentials('docker-login-pwd')
    }
    agent {
        docker {
            image 'gnschenker/node-docker'
            args '-p 3000:3000'
            args '-w /app'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            step {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build & Push Docker image') {
            steps {
                sh 'docker image build -t $registry:$BUILD_NUMBER .'
                sh 'docker login -u hegistva -p $DOCKER_PWD'
                sh 'docker image push $registry:$BUILD_NUMBER'
                sh 'docker image rm $registry:$BUILD_NUMBER'
            }
        }
        stage('Deploy and smoke test') {
            steps {
                sh './jenkins/scripts/deploy.sh'
            }
        }
        stage('Cleanup') {
            steps {
                sh './jenkins/scripts/cleanup.sh'
            }
        }
    }
}