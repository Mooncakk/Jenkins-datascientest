pipeline{
    agent any
    stages {
        stage ('deploy') {
            when {
                branch 'stage'
            }
            steps {
                sh 'echo deploy stage to main'
            }
        }
    }
}