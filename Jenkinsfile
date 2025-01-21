pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('GITHUB_TOKEN')  // Assuming token is stored securely in Jenkins
    }

    stages {
        stage('Check Token') {
            steps {
                script {
                    // Make sure token is correctly passed
                    echo "Token is set"
                }
            }
        }

        stage('Create GitHub Repo') {
            steps {
                script {
                    // Login using GitHub CLI with the token from environment
                    sh '''
                        gh auth login --with-token <<< "$GITHUB_TOKEN"
                    '''

                    // Create the repository
                    sh '''
                        gh repo create sivacloudops59/JAVA --private --confirm
                    '''
                }
            }
        }
    }
}
