pipeline{
    agent any
    environment{
        NETLIFY_SITE_ID = '9d6c5b3b-1db3-4176-aaee-c18ae360950a'
        NETLIFY_AUTH_TOKEN = credentials('jenkins')
    }
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
            stage('Deploy STG'){
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
                    echo 'Deploying to STG environment...'
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build 
                    '''
                }
            }
           
            stage('Deploy PROD'){
                agent{
                    docker{
                        image 'node:18-alpine'
                        reuseNode true
                    }
                }
                steps{
                     timeout(time:"1",units:"MINUTES"){
                        input message: 'Are you sure you want to deploy to production?', ok: 'Deploy'
                    }
                    sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    node_modules/.bin/netlify status
                    echo 'Deploying to PROD environment...'
                    node_modules/.bin/netlify deploy --dir=build --prod
                    '''
                }
            }
        }
    
}