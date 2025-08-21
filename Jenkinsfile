pipeline {
    agent any

    stages {
        /*stage('Build'){
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
        */
        stage('Tests'){
            parallel{
                stage('Unit Test'){
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
                        npm test
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
                    post {
                        always{
                            junit 'jest-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                            // cleanWs()
                            }
                        }
                    }
                }

                stage('E2E'){
                    agent{
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                            }
                        }
                    steps{
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always{
                            junit 'jest-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                            // cleanWs()
                        }
                    }
                }
            }
        }
    }