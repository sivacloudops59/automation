pipeline {
    agent any
    parameters {
        string(name: 'GITHUB_REPO', description: 'Name of the GitHub repository to create')
        string(name: 'SOURCE_BRANCH', description: 'Name of the source branch to cut the new branch from (default: main)')
        string(name: 'NEW_BRANCH', description: 'Name of the new branch to create and protect')
    }
    environment {
        GITHUB_TOKEN = credentials('github_token') // Your GitHub token stored in Jenkins
        GITHUB_OWNER = 'sivacloudops59' // Your GitHub username or organization
    }
    stages {
        stage('Create GitHub Repository') {
            steps {
                script {
                    sh """
                    gh repo create \$GITHUB_OWNER/\$GITHUB_REPO --public --confirm --description "Automated public repo creation via Jenkins pipeline" --add-readme
                    """
                }
            }
        }
        stage('Create and Push Branch from Source') {
            steps {
                script {
                    sh """
                    git clone https://github.com/\$GITHUB_OWNER/\$GITHUB_REPO.git
                    cd \$GITHUB_REPO
                    git fetch origin \$SOURCE_BRANCH || exit 0
                    if [ ! -f README.md ]; then
                        echo "# Initial commit" > README.md
                        git add README.md
                        git commit -m "Initial commit"
                        git push origin main
                    fi
                    git checkout -b \$NEW_BRANCH
                    git push origin \$NEW_BRANCH
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
            echo 'Public GitHub repository, branch, and protection created successfully!'
        }
        failure {
            echo 'Failed to automate GitHub tasks.'
        }
    }
}
