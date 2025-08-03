pipeline {
    agent any
    stages {
        stage ('testdocker') {
            steps {
                script {
                    docker.image('hello-world:latest').withRun { c ->
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