pipeline {
    agent any

    stages {
        stage('Without Docker'){
            steps{
                sh '''
                    echo "Without Docker"
                    ls -la
                    touch container-no.txt
                '''
            }
        }
        stage('with docker') {
            agent{
                docker {
                    image "node:18-alpine"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "With docker"
                    ls -la
                    touch container-yes.txt
                    '''
            }
        }
    }
}