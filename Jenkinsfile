pipeline {
    agent any
    parameters {
        string(name: 'EXISTING_REPO', defaultValue: 'automation', description: 'Name of the existing GitHub repository')
        string(name: 'NEW_REPO', description: 'Name of the new GitHub repository to create')
        string(name: 'SOURCE_BRANCH', defaultValue: 'main', description: 'Branch in the existing repository to copy')
        string(name: 'NEW_BRANCH', description: 'Name of the new branch to create in the new repository')
    }
    environment {
        GITHUB_TOKEN = credentials('github_token') // GitHub token stored in Jenkins
        GITHUB_OWNER = 'sivacloudops59' // Your GitHub username
    }
    stages {
        stage('Create New GitHub Repository') {
            steps {
                script {
                    sh """
                    # Create the new repository
                    gh repo create \$GITHUB_OWNER/\$NEW_REPO --public --confirm --description "New repo created via Jenkins pipeline"
                    """
                }
            }
        }
        stage('Clone Existing Repository') {
            steps {
                script {
                    sh """
                    # Clone the existing repository
                    git clone https://\$GITHUB_OWNER:\$GITHUB_TOKEN@github.com/\$GITHUB_OWNER/\$EXISTING_REPO.git
                    
                    # Navigate to the repository directory
                    cd \$EXISTING_REPO
                    
                    # Fetch and checkout the source branch
                    git fetch origin \$SOURCE_BRANCH
                    git checkout \$SOURCE_BRANCH
                    """
                }
            }
        }
        stage('Create and Push New Branch to New Repository') {
            steps {
                script {
                    sh """
                    # Create a new branch
                    cd \$EXISTING_REPO
                    git checkout -b \$NEW_BRANCH
                    
                    # Change the remote to the new repository
                    git remote set-url origin https://\$GITHUB_OWNER:\$GITHUB_TOKEN@github.com/\$GITHUB_OWNER/\$NEW_REPO.git
                    
                    # Push the new branch to the new repository
                    git push -u origin \$NEW_BRANCH
                    """
                }
            }
        }
    }
    post {
        success {
            echo "Successfully created the repository '\$NEW_REPO' and pushed the branch '\$NEW_BRANCH'!"
        }
        failure {
            echo "Failed to complete the pipeline tasks."
        }
    }
}
