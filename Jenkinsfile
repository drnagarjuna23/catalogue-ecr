pipeline {
    // These are pre-build sections
    agent {
        node {
            label 'AGENT-1'
        }
    }
    environment {
        COURSE = "Jenkins"
        appVersion = ""
        ACC_ID = "230937596690"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }
    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
    // This is build section
    stages {
        stage('Read Version') {
            steps {
                script{
                       def packageJSON = readJSON file: 'package.json'
                       appVersion = packageJSON.version
                       echo "app version: ${appVersion}"
                }
            }
        }
        stage('Install dependencies') {
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Build Image') {
            steps {
                script {
                    sh """
                        docker build -t catalogue:${appVersion} .
                        docker tag catalogue:${appVersion} \
                        230937596690.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${appVersion}
                    """
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region us-east-1 | \
                        docker login --username AWS --password-stdin \
                        230937596690.dkr.ecr.us-east-1.amazonaws.com

                        docker push \
                        230937596690.dkr.ecr.us-east-1.amazonaws.com/roboshop/catalogue:${appVersion}
                    """
                }
            }
        }
    }
    post{
        always{
            echo 'I will always say Hello again!'
            cleanWs()
        }
        success {
            echo 'I will run if success'
        }
        failure {
            echo 'I will run if failure'
        }
        aborted {
            echo 'pipeline is aborted'
        }
    }
}