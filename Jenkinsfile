pipeline{
    agent any
    stages{
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
        stage("Test'"){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
        steps{
            sh'''
                echo "Test stage"
                test -f build/index.html
                npm test
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
