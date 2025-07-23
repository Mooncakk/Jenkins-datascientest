pipeline{
    agent any
    stages {
        stage ('deploy') {
            when {
                branch 'refs/heads/stage'
            }
            steps {
                sh 'echo deploy stage to main'
            }
        }
    }
}