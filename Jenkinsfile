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
                    echo "Building the application..."
                    npm install
                    '''
                }
            }
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
        }
    
}