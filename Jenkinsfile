pipeline{
    agent any
    environment{
        NETLIFY_SITE_ID = '9d6c5b3b-1db3-4176-aaee-c18ae360950a'
        NETLIFY_AUTH_TOKEN = credentials('jenkins')
        
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
             stage("aws"){
                agent{
                    docker{
                        image 'amazon/aws-cli'
                        args '--entrypoint=""'
                    }
                }
                environment{
                    AWS_S3_BUCKET = 'jenkinspracbucket'
                }
                steps{
                    withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                        sh '''
                        aws --version
                        echo "Configuring AWS CLI..."
                        aws s3 sync build s3://${AWS_S3_BUCKET}
                        '''
                           }
                    
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
                        image 'react-app'
                        reuseNode true
                    }
                }
                steps{
                    sh '''
                    echo "Running E2E..."
                    npm install serve
                    serve -s build &
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