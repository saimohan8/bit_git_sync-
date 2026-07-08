pipeline {

    agent any

    environment {
        BITBUCKET_REPO = 'https://msaimohan8@bitbucket.org/saimohanproject/second.git'
        GITHUB_REPO = 'github.com/saimohan8/bit_git_sync-.git'
        GITHUB_CREDENTIALS = 'github-token'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Clone Bitbucket Repository') {
            steps {

                withCredentials([
                    string(credentialsId: 'bitbucket-secret', variable: 'BITBUCKET_TOKEN')
                ]) {

                    sh '''
                        echo "===== Cloning Bitbucket Repository ====="

                        # Insert token into the Bitbucket URL
                        CLONE_URL=$(echo ${BITBUCKET_REPO} | sed "s#https://msaimohan8@#https://x-token-auth:${BITBUCKET_TOKEN}@#")

                        echo "Cloning Repository..."

                        git clone --mirror "$CLONE_URL"

                        echo "Clone Completed Successfully"
                    '''
                }
            }
        }

        stage('Verify Repository') {
            steps {

                sh '''
                    cd second.git

                    echo "===== Branches ====="
                    git branch -a

                    echo "===== Tags ====="
                    git tag

                    echo "===== Remotes ====="
                    git remote -v
                '''
            }
        }

        stage('Push to GitHub') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: "${GITHUB_CREDENTIALS}",
                        usernameVariable: 'GITHUB_USER',
                        passwordVariable: 'GITHUB_TOKEN'
                    )
                ]) {

                    sh '''
                        cd second.git

                        git remote add github https://${GITHUB_USER}:${GITHUB_TOKEN}@${GITHUB_REPO}

                        echo "Pushing All Branches..."

                        git push --mirror github
                    '''
                }
            }
        }
    }

    post {

        success {
            echo 'Repository Successfully Migrated.'
        }

        failure {
            echo 'Repository Migration Failed.'
        }

        always {
            cleanWs()
        }
    }
}