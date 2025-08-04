pipeline{
    agent any

    environment {
                github_token  = credentials('github_tok')
    }

    stages {
        stage ('build') {
            steps {
                echo '''
                python -m venv env
                pip install -r requirements.txt
                '''
            }
        }

        stage ('merging') {
            steps {
                script {
                    def link = "https://api.github.com/repos/mooncakk/Jenkins-datascientest/merges"
                    sh '''
                    echo "deploy staging to main"
                    curl -X POST \
                    -H "Authorization: token ${github_token}"
                    -H "Accept: application/vnd.github.v3+json"
                    echo "${link}"
                    -d '{
                    "base": "main",
                    "head": "staging",
                    "commit_message": "Direct merge of staging into main"
                    '''
                }

            }
        }
    }
}