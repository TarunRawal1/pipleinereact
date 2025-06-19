pipeline{
    agent any
    environment{
        NETLIFY_SITE_ID = '9d6c5b3b-1db3-4176-aaee-c18ae360950a'
        NETLIFY_AUTH_TOKEN = credentials('jenkins')
        REACT_APP_VERSION = '1.0.0'
    }
        stages{
            stage("docker"){
                steps{
                    sh 'docker build -t react-app .'
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
                    echo "Building the application..."
                    npm install
                    npm run build
                    '''
                }
            }
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
                        image 'react-app'
                        reuseNode true
                    }
                }
                steps{
                    sh '''
                    netlify --version
                    echo 'Deploying to STG environment...'
                    netlify status
                    netlify deploy --dir=build --json > deploy-output.json
                    node-jq -r '.deploy_url' deploy-output.json 
                    '''
                }
            }
           
            stage('Deploy PROD'){
                agent{
                    docker{
                        image 'react-app'
                        reuseNode true
                    }
                }
                steps{
                    sh '''
                    netlify --version
                    netlify status
                    echo 'Deploying to PROD environment...'
                    netlify deploy --dir=build --prod
                    '''
                }
            }
        }
    
}