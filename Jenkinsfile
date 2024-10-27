pipeline{
    // this is comment
    agent any
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
        stage("Test'"){
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
        }
        stage('E2E'){
            agnet{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.48.1-noble'
                }
            }
            steps{
                sh'''
                echo "in E2E stage"
                npm install -g serve
                serve -s build

                '''
            }
        }
    }  
    post{
        always{
            junit 'test-results/junit.xml'
        }
    } 
}
