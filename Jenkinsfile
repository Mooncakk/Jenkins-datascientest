pipeline{
    agent any
    stages {
        stage('Debug Branch Info') {
            steps {
                script {
                    echo "Current branch: ${env.BRANCH_NAME}"
                    echo "Git branch: ${env.GIT_BRANCH}"
                    sh 'git branch -a'
                    sh 'echo "Current HEAD: $(git rev-parse --abbrev-ref HEAD)"'
                }
            }
        }

        stage ('deploy') {
            when {
                branch 'origin/stage'
            }
            steps {
                sh 'echo deploy stage to main'
            }
        }
    }
}