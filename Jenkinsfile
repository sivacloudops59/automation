pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('GITHUB_TOKEN')
    }

    stages {
        stage('Check Token') {
            steps {
                script {
                    // Make sure token is correctly passed
                    echo "GITHUB_TOKEN: $GITHUB_TOKEN"
                }
            }
        }

        stage('Create GitHub Repo') {
            steps {
                script {
                    // Use here-string to pass token securely to gh
                    sh "echo $GITHUB_TOKEN | gh auth login --with-token"
                    
                    // Create the repository
                    sh "gh repo create sivacloudops59/JAVA --private --confirm"
                }
            }
        }
    }
}
