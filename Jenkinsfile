pipeline{
    agent any

    environment {
                GITHUB_TOKEN  = credentials('github_tok')
                REPO_OWNER = 'mooncakk'
                REPO_NAME = 'jenkins-datascientest'
    }
    parameters {
        string(
            name: 'BRANCH_NAME',
            defaultValue: 'STAGING',
            description: 'Source branch for the pull request'
        )
        string(
            name: 'BASE_BRANCH',
            defaultValue: 'MAIN',
            description: 'Target branch for the pull request'
        )
        string(
            name: 'MERGE_METHOD',
            defaultValue: 'squash',
            description: 'Auto-merge method to use'
        )
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
        stage('Create Pull Request') {
            steps {
                script {
                    echo "Creating pull request from ${params.BRANCH_NAME} to ${params.BASE_BRANCH}"

                    // Step 1: Create PR using GitHub REST API
                    def prResponse = httpRequest(
                        httpMode: 'POST',
                        url: "https://api.github.com/repos/${REPO_OWNER}/${REPO_NAME}/pulls",
                        customHeaders: [
                            [name: 'Authorization', value: "Bearer ${GITHUB_TOKEN}"],
                            [name: 'Accept', value: 'application/vnd.github.v3+json']
                        ],
                        contentType: 'APPLICATION_JSON',
                        validResponseCodes: '200:299',
                        requestBody: """{
                            "title": "Auto merge to main",
                            "body": "## Auto-Merge:\\nThis PR will automatically merge when all checks pass.\\n\\n---\\n*Created by Jenkins Build #${BUILD_NUMBER}*",
                            "head": "${params.BRANCH_NAME}",
                            "base": "${params.BASE_BRANCH}"
                        }"""
                    )
                    // Extract PR details from response
                    def prData = readJSON text: prResponse.content
                    env.PR_NUMBER = prData.number
                    env.PR_NODE_ID = prData.node_id
                    //env.PR_URL = prData.html_url

                    echo "Pull Request created successfully!"
                    echo "PR Number: #${env.PR_NUMBER}"
                    //echo "🔗 PR URL: ${env.PR_URL}" A SUPPRIMER
                    //echo "🆔 Node ID: ${env.PR_NODE_ID}"
                }
            }
        }
        stage('Enable Auto-Merge') {
            steps {
                script {
                    echo "Enabling auto-merge with method: ${params.MERGE_METHOD}"

                    // Step 2: Enable auto-merge using GitHub GraphQL API
                    def autoMergeResponse = httpRequest(
                        httpMode: 'POST',
                        url: 'https://api.github.com/graphql',
                        customHeaders: [
                            [name: 'Authorization', value: "Bearer ${GITHUB_TOKEN}"],
                            [name: 'Content-Type', value: 'application/json']
                        ],
                        validResponseCodes: '200:299',
                        requestBody: """{
                            "query": "mutation { enablePullRequestAutoMerge(input: { pullRequestId: \\"${env.PR_NODE_ID}\\", mergeMethod: ${params.MERGE_METHOD} }) { pullRequest { autoMergeRequest { enabledAt enabledBy { login } } } } }"
                        }"""
                    )

                    // Parse auto-merge response
                    def autoMergeData = readJSON text: autoMergeResponse.content

                    if (autoMergeData.data?.enablePullRequestAutoMerge?.pullRequest?.autoMergeRequest) {
                        def autoMergeRequest = autoMergeData.data.enablePullRequestAutoMerge.pullRequest.autoMergeRequest
                        env.AUTO_MERGE_ENABLED_AT = autoMergeRequest.enabledAt
                        env.AUTO_MERGE_ENABLED_BY = autoMergeRequest.enabledBy.login

                        //echo "✅ Auto-merge enabled successfully!" A SUPPRIMER
                        //echo "⏰ Enabled at: ${env.AUTO_MERGE_ENABLED_AT}"
                        //echo "👤 Enabled by: ${env.AUTO_MERGE_ENABLED_BY}"

                        // Add informational comment to PR
                        httpRequest(
                            httpMode: 'POST',
                            url: "https://api.github.com/repos/${REPO_OWNER}/${REPO_NAME}/issues/${env.PR_NUMBER}/comments",
                            customHeaders: [
                                [name: 'Authorization', value: "Bearer ${GITHUB_TOKEN}"]
                            ],
                            contentType: 'APPLICATION_JSON',
                            requestBody: """{
                                "body": "**Auto-Merge Enabled!**\\n\\n**Method:** ${params.MERGE_METHOD}\\n **Enabled:** ${env.AUTO_MERGE_ENABLED_AT}\\n🔧 **By:** ${env.AUTO_MERGE_ENABLED_BY}\\n\\n**Status:** This PR will automatically merge when all required checks pass.\\n\\n---\\n*Managed by Jenkins Build #${BUILD_NUMBER}*"
                            }"""
                        )

                    } else {
                        echo "Failed to enable auto-merge"
                        echo "Response: ${autoMergeResponse.content}"
                        error("Auto-merge setup failed")
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                echo """
                SUCCESS: Pull Request with Auto-Merge Created!

                Summary:
                - PR Number: #${env.PR_NUMBER}
                - PR URL: ${env.PR_URL}
                - Source Branch: ${params.BRANCH_NAME}
                - Target Branch: ${params.BASE_BRANCH}
                - Auto-Merge: Enabled (${params.MERGE_METHOD})
                - Enabled At: ${env.AUTO_MERGE_ENABLED_AT}
                """
            }
        }
        failure {
            script {
                echo "Pipeline failed during PR creation or auto-merge setup"

                if (env.PR_NUMBER) {
                    echo "PR was created but auto-merge setup failed"
                    echo "Manual intervention may be required"
                    echo "PR URL: ${env.PR_URL}"
                } else {
                    echo "PR creation failed - check GitHub API access and branch existence"
                }
            }
        }
    }
}