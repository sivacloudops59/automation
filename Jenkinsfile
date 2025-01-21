pipeline {
    agent any
    parameters {
        string(name: 'GITHUB_REPO', description: 'Name of the GitHub repository to create')
        string(name: 'SOURCE_BRANCH', description: 'Name of the source branch to cut the new branch from')
        string(name: 'NEW_BRANCH', description: 'Name of the new branch to create and protect')
    }
    environment {
        GITHUB_TOKEN = credentials('github_token') // Your GitHub token stored in Jenkins
        GITHUB_OWNER = 'sivacloudops59' // Change to your GitHub username or organization
    }
    stages {
        stage('Create GitHub Repository') {
            steps {
                script {
                    sh """
                    echo \$GITHUB_TOKEN | gh auth login --with-token || exit 1
                    gh repo create \$GITHUB_OWNER/\$GITHUB_REPO --private --confirm || exit 1
                    """
                }
            }
        }
        stage('Create and Push Branch from Source') {
            steps {
                script {
                    sh """
                    git clone https://github.com/\$GITHUB_OWNER/\$GITHUB_REPO.git || exit 1
                    cd \$GITHUB_REPO
                    git fetch origin \$SOURCE_BRANCH || exit 1
                    git checkout -b \$NEW_BRANCH origin/\$SOURCE_BRANCH || exit 1
                    git push origin \$NEW_BRANCH || exit 1
                    """
                }
            }
        }
        stage('Set Branch Protection') {
            steps {
                script {
                    sh """
                    gh api -X PUT /repos/\$GITHUB_OWNER/\$GITHUB_REPO/branches/\$NEW_BRANCH/protection --input - <<EOF
                    {
                      "required_status_checks": null,
                      "enforce_admins": true,
                      "required_pull_request_reviews": {
                        "dismiss_stale_reviews": true,
                        "require_code_owner_reviews": true,
                        "required_approving_review_count": 1
                      },
                      "restrictions": null
                    }
                    EOF
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'GitHub repository, branch, and protection created successfully!'
        }
        failure {
            echo 'Failed to automate GitHub tasks.'
        }
    }
}
