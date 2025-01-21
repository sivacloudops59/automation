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
        stage('Install GitHub CLI') {
            steps {
               script {
                    sh """
                    apt-get update && apt-get install -y \
                    curl \
                    apt-transport-https \
                    software-properties-common && \
                    curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | \
                    dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg && \
                    echo "deb [arch=\$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | \
                    tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
                    apt-get update && apt-get install -y gh
                    """
                }
           }
        }
        stage('Create GitHub Repository') {
            steps {
                script {
                    sh """
                    echo \$GITHUB_TOKEN | gh auth login --with-token
                    gh repo create \$GITHUB_OWNER/$GITHUB_REPO --private --confirm
                    """
                }
            }
        }
        stage('Create and Push Branch from Source') {
            steps {
                script {
                    sh """
                    git clone https://github.com/\$GITHUB_OWNER/$GITHUB_REPO.git
                    cd $GITHUB_REPO
                    git fetch origin $SOURCE_BRANCH
                    git checkout -b $NEW_BRANCH origin/$SOURCE_BRANCH
                    git push origin $NEW_BRANCH
                    """
                }
            }
        }
        stage('Set Branch Protection') {
            steps {
                script {
                    sh """
                    gh api -X PUT /repos/\$GITHUB_OWNER/$GITHUB_REPO/branches/$NEW_BRANCH/protection --input - <<EOF
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