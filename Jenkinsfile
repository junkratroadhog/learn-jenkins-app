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
                '''
            }
        }
        stage('Test'){
            steps{

                // TEST
                sh '''
                    pwd
                    echo "$WORKSPACE"
                    ls -ltr
                    if [ -f './build/index.html' ]; then
                        echo "index.html Found"
                    else
                        echo "index.html Not Found"
                    fi
                    npm test -a
                '''
            }
        }    
    }
}