pipeline{
    // this is comment
    agent any

    environment{
        NETLIFY_SITE_ID = '97cecb3f-1054-4454-9883-8c2c778c7005'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        }
    stages{
        /*
        
        stage('Build'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                echo " npm starts"
                node --version
                npm --version
                ls -la
                npm ci
                npm run build
                ls -la
                '''
            }

        }
        */
        stage("Tests"){
            parallel{
                    stage("Unit Test"){
                        agent{
                            docker{
                                image 'node:18-alpine'
                                reuseNode true
                            }
                        }
                        steps{
                            sh'''
                                #echo "Test stage"
                                #test -f build/index.html
                                npm test
                            '''
                        }
                    post{
                        always{
                            junit 'jest-results/junit.xml'
                        }
                    } 
                    }
                    
                   stage("Unit E2E"){
                        agent{
                            docker{
                                image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                reuseNode true
                            }
                        }
                        steps{
                            sh'''
                            echo "in Unit E2E stage"
                        
                            npm install  serve

                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html

                            '''
                        }
                        post{
                            always{
                                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                            }
                    } 
                }
                    
                }  
            }
        stage(" Stage Deploy"){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                npm install netlify-cli node-jq
                node_modules/.bin/netlify --version
                echo "Deploying to production :Site id: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                node_modules/.bin/node-jq -r '.deploy-url' deploy-output.json
                '''
            }

        }
         stage("Approval"){
            steps{
                timeout(time:1,unit:'MINUTES')
                 input message: 'are you sure ', ok: 'okay, I am sure'
            }
        }
        stage("Prod Deploy"){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                npm install netlify-cli
                node_modules/.bin/netlify --version
                echo "Deploying to production :Site id: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }

        }
       
        stage("Prod E2E"){
                agent{
                    docker{
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                    }
                }
                environment{
                    CI_ENVIRONMENT_URL='https://jolly-brioche-529299.netlify.app'
                }
                steps{
                    sh'''
                    echo "in Prod E2E stage"
                    npx playwright test --reporter=html

                    '''
                }
                post{
                    always{
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
            } 
        }
    }
        
}
