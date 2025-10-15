pipeline {
    // Use Docker Node image to ensure Node & npm are available
    agent { docker { image 'node:18-bullseye' } }

    environment {
        // Replace with the ID of your Jenkins GitHub credential
        GH_TOKEN_CREDS = 'github-token'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install htmlhint --no-save'
            }
        }

        stage('Test HTML') {
            steps {
                sh 'npx htmlhint html/**/*.html'
            }
        }

        stage('Deploy to GitHub Pages') {
            steps {
                // Use GitHub token from Jenkins credentials
                withCredentials([string(credentialsId: "${GH_TOKEN_CREDS}", variable: 'GITHUB_TOKEN')]) {
                    sh '''
                    git config --global user.email "jenkins@example.com"
                    git config --global user.name "Jenkins CI"

                    # Create or switch to gh-pages branch
                    git checkout --orphan gh-pages || git checkout gh-pages
                    git rm -rf .

                    # Copy site files
                    cp -r html/* .

                    git add -A
                    git commit -m "Deploy from Jenkins"

                    # Push using GitHub token
                    REPO_URL="https://x-access-token:${GITHUB_TOKEN}@github.com/sharma-shrestha/MonumentsIndia.git"
                    git push -f $REPO_URL gh-pages
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check console output."
        }
    }
}
