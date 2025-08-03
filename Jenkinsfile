pipeline {
    agent { docker { image 'maven:3.8.7-eclipse-temurin-11' } }
    stages {
        stage('testbuild') {
            steps {
                sh 'mvn --version'
            }
        }
        stage ('testdocker') {
            steps {
                script {
                    def hello = docker.image('alpine:latest')
                    hello.withRun { c ->
                    sh 'echo je suis à l intérieure!!!'}
                }
            }
        }
        stage ('build') {
            steps {
                echo 'pip install -r !!!'
            }
        }
        stage ('test') {
            steps {
                echo 'Test executed !!!'
            }
        }
    }
}