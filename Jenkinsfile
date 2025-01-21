pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('GITHUB_TOKEN')
    }

    stages {
        stage('Check Token') {
            steps {
                script {
                    // Print token to confirm it's being passed
                    echo "GITHUB_TOKEN: $GITHUB_TOKEN"
                }
            }
        }

        stage('Create GitHub Repo') {
            steps {
                script {
                    // Log in using the token
                    sh 'echo $GITHUB_TOKEN | gh auth login --with-token'

                    // Create the repository
                    sh 'gh repo create sivacloudops59/JAVA --private --confirm'
                }
            }
        }
    }
}
