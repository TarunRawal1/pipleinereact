Pipeline{
    any agent
    {
        Stages{
            Stage("Build"){
                agent{
                    docker{
                        image 'node:18-alpine'
                    }
                }
                steps{
                    sh '''
                    echo "Building the application..."
                    npm install
                    '''
                }
            }
            Stage("Test"){
                agent{
                    docker{
                        image 'node:18-alpine'
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
}