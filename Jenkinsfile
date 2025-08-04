pipeline{
    agent any

    environment {
                github_token  = credential('github_tok')
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
            sh '''
            echo "deploy staging to main"
            curl -X POST \
            -H "Authorization: token ${github_tokenq}"
            -H "Accept: application/vnd.github.v3+json"
            https://api.github.com/repos/mooncakk/Jenkins-datascientest/merges
            -d '{
            "base": "main",
            "head": "staging",
            "commit_message": "Direct merge of staging into main"
            '''
            }
        }
    }
}