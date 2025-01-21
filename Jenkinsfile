pipeline {
    agent any
    parameters {
        string(name: 'GITHUB_REPO', description: 'Name of the GitHub repository to create')
        string(name: 'SOURCE_BRANCH', description: 'Name of the source branch to cut the new branch from')
        string(name: 'GITHUB_BRANCH', description: 'Name of the branch to create and protect')
    }
    environment {
        GITHUB_TOKEN = credentials('github_token') // Your GitHub token stored in Jenkins
        GITHUB_OWNER = 'your-github-org-or-username' // Change to your GitHub username or organization
    }
    stages {
        stage('Create GitHub Repository') {
            steps {
                script {
                    sh """
                    gh auth login --with-token <<< \$GITHUB_TOKEN
                    gh repo create \$GITHUB_OWNER/$GITHUB_REPO --private --confirm
                    """
                }
            }
        }
        stage('Create and Push Branch') {
            steps {
                script {
                    sh """
                    git clone https://github.com/\$GITHUB_OWNER/$GITHUB_REPO.git
                    cd $GITHUB_REPO
                    git checkout -b $GITHUB_BRANCH
                    git push origin $GITHUB_BRANCH
                    """
                }
            }
        }
        stage('Set Branch Protection') {
            steps {
                script {
                    sh """
                    gh api -X PUT /repos/\$GITHUB_OWNER/$GITHUB_REPO/branches/$GITHUB_BRANCH/protection --input - <<EOF
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
