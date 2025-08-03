pipeline {
    agent any
    stages {
        stage ('testdocker') {
            steps {
                script {

                    def hello = docker.image('alpine:latest')
                    hello.pull()
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