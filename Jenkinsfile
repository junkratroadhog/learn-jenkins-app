pipeline {
    agent any

    stages {
        stage('Cleanup'){
            steps{
                cleanWs()
            }
        }

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
            agent any
            steps{
                sh '''
                    pwd
                    cd $Workspace
                    ls -ltr
                    '''
                    
                    if {fileExists('$WORKSPACE/build/index.html')} {
                        echo "index.html Found"
                    } else {
                        echo "index.html Not Found"
                    }
                
            }
        }
    }
}