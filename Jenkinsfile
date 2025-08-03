pipeline {
    agent any
    stages {
        stage ('testdocker') {
            steps {
                script {
                    docker.image('hello-world:latest')
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