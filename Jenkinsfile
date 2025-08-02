pipeline {
    agent any
    stages {
        stage ('test') {
            steps {
                echo 'Test executed'
                when {
                    branch 'staging'
                }
            }
        }
    }
}