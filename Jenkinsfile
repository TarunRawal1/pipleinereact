pipeline{
    agent any
        stages{
            // stage('Build'){
            //     agent{
            //         docker{
            //             image 'node:18-alpine'
            //             reuseNode true
            //         }
            //     }
            //     steps{
            //         sh '''
            //         echo "Building the application..."
            //         npm install
            //         npm run build
            //         '''
            //     }
            // }
            stage("tests"){
                parallel{
                     stage('Test'){
                agent{
                    docker{
                        image 'node:18-alpine'
                        reuseNode true
                    }
                }
                steps{
                    sh '''
                    echo "Running tests..."
                    npm install
                    npm run build
                    npm test
                    '''
                }
            }
            stage('E2E'){
                agent{
                    docker{
                        image 'mcr.microsoft.com/playwright:v1.53.0-noble'
                        reuseNode true
                    }
                }
                steps{
                    sh '''
                    echo "Running E2E..."
                    npm install serve
                    node_modules/.bin/serve -s build &
                    '''
                }
            }
                }
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
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    '''
                }
            }
        }
    
}