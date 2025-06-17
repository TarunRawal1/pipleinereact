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
                    sleep 5
                    npx playwright test
                    '''
                }
            }
        }
    
}