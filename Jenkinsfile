pipeline {

    agent any
    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        BITBUCKET_REPO = 'https://msaimohan8@bitbucket.org/saimohanproject/second.git'
        GITHUB_REPO = 'https://github.com/saimohan8/firstone.git'
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

                        CLONE_URL=$(echo "$BITBUCKET_REPO" | sed "s#https://msaimohan8@#https://x-token-auth:${BITBUCKET_TOKEN}@#")

                        git clone "$CLONE_URL"

                        echo "===== Clone Successful ====="
                    '''
                }
            }
        }

        stage('Verify Repository') {
            steps {
                sh '''
                    cd second

                    echo "===== Local Branches ====="
                    git branch

                    echo "===== Remote Branches ====="
                    git branch -r

                    echo "===== Tags ====="
                    git tag

                    git remote -v
                '''
            }
        }

        stage('Sync All Branches to GitHub') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: "${GITHUB_CREDENTIALS}",
                        usernameVariable: 'GITHUB_USER',
                        passwordVariable: 'GITHUB_TOKEN'
                    )
                ]) {

                    sh '''
                        cd second

                        git remote remove github || true

                        git remote add github https://${GITHUB_USER}:${GITHUB_TOKEN}@github.com/saimohan8/bit_git_sync-.git

                        git fetch origin

                        git branch -r | grep "origin/" | grep -v "HEAD" | while read remote
                        do
                            branch=$(echo "$remote" | sed 's#origin/##' | xargs)

                            echo "======================================"
                            echo "Syncing Branch : $branch"
                            echo "======================================"

                            git checkout -B "$branch" "origin/$branch"

                            git push github "$branch" --force
                        done

                        echo "===== Pushing Tags ====="

                        git push github --tags --force
                    '''
                }
            }
        }
    }

    post {

        success {
            echo "======================================"
            echo "Bitbucket Successfully Synced to GitHub"
            echo "======================================"
        }

        failure {
            echo "======================================"
            echo "Synchronization Failed"
            echo "======================================"
        }

        always {
            cleanWs()
        }
    }
}