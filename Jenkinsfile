pipeline {
    agent any

    stages {
        stage('Build'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                    npm test -a
                '''
            }
        }
        stage('Test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{

                // TEST
                sh '''
                    pwd
                    echo "$WORKSPACE"
                    ls -ltr
                    npm test -a
                '''
                sh '''
                    if [ -f './build/index.html' ]; then
                        echo "index.html Found"
                    else
                        echo "index.html Not Found"
                        exit 1
                    fi
                '''
            }
        }    
    }
}