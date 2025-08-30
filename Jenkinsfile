pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = "f79169ab-9655-4161-99d2-3b88b6ddb1a6"
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
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
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright local', reportTitles: '', useWrapperFileDirectly: true])
                            // cleanWs()
                        }
                    }
                }
            }
        }

        stage {
            input 'Approve Deployment?' ok: 'Yes, I am approving it to proceed'
        }

        stage('Deploy'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                npm install netlify-cli@20.1.1
                node_modules/.bin/netlify --version
                echo "Deploying to Netlify... SITE ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
                echo "Small change"
                '''
            }
        }

        stage('Production E2E'){
            agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    }
                }
            environment{
                    CI_ENVIRONMENT_URL = 'https://calm-beijinho-eb3037.netlify.app'
                }

            steps{
                sh '''
                    npx playwright test --reporter=html
                '''
            }
            post {
                always{
                    junit 'jest-results/junit.xml'
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E Report', reportTitles: '', useWrapperFileDirectly: true])
                    // cleanWs()
                }
            }
        }
    }
}

