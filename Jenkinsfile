pipeline {
    agent any
     environment {
     
      AWS_ACCESS_KEY_ID = "ASIAUKCG3R4YKOV57CI2"
    AWS_SECRET_ACCESS_KEY = "6InLI7VWvyXYUQmJtbVLzRtJhbpBWC7m3lTciWYm"
    AWS_SESSION_TOKEN = "IQoJb3JpZ2luX2VjELj//////////wEaCXVzLXdlc3QtMiJHMEUCIQD0boWjcxgMFVjh+ebHSirbnesFyJrfgsxJqb9Nrr3C8AIgDwvBmwnimuGiDkgKwkCYq/ra1+5Z5Ra+n3B/gqTu2eAqngMIQRAAGgwyOTY1MDEzNTAxOTIiDL3cAZ2vWZ18rItcAyr7Au1/QY1Z/zR9+I29NgE7WsFpEfXAMXCUsAoshLitaWIy44RDeTZNFMcOksNB7SgJa+fA+mBgxZsh6/eoX2DDZTxQ1T9Muz5jEe1htPCCx3KUqc+cwmAQxMMzm8NQs7YiD6vDZte2yHhMzTWNIAv1gyzVhfKysOzPgw8U2FA2KVwhYugHeSyPrAN6jn9cgOh9amcGfx0s7P62p2+yVM82az1KRZegCDMM/pl0hM9E9Yue2J3AmkhcvKwkQnk8VRoYm35EcJtPNHWR0D00SSWj9NI1aSE5PSnpD4hlp8cJz9NZBSSd3KjC4kh6bhnS8KIRQHpOCSsz52b4G8wH5wOCDpYUmWs5Bwxo4J/1IY8hAspB4hNBu1W7TsY1hsXkl7NpIoDOAUXhFGiZcz23o7TZBZvbRX5PVDlORvIE34XstdJW7H0lWzPmrhY5w1UI/wWnZ4Ob0Wd5KeGl+BSL/xusjlMi27Phs5ymbSixUPz2Vj9M6zSKBH5NQk1YR3Ew37zOpQY6pgF6x+s2aZ+aHFp9vg6mXDyufc3C4hXeE4lDubysN4syd/TDzihUGZTkJYGNxnf2ectLZ+31Dw3knU068xzPe/F5B89MIM/4njc/3O4/LfKLOFFFJAmb1uSsbpy0CJOOSS5b19v6MqqhN/lAhgmjA3q25ZrdcHnPLNdVbHhrX2/NH/DIPNbSt0+7WlUz21HrPKYI0EWqH6Ffw+VCpxSifufvNA4i8/6J"
     
                BUCKET_NAME = 'devops-test-aws-mp-v3-log'
                LAMBDA_FUNCTION_NAME = "test"
                LAMBDA_LAYER_NAME = "my-layername"
                REGION = "us-east-1"
            }
    stages {
        stage('Deploy Lambda Function and Layers') {
            steps {
                script {
                      // Export the environment variables
                      //env.AWS_ACCESS_KEY_ID = AWS_ACCESS_KEY_ID
                      //env.AWS_SECRET_ACCESS_KEY = AWS_SECRET_ACCESS_KEY
                      //env.AWS_SESSION_TOKEN = AWS_SESSION_TOKEN
                      // Create a deployment package for the Lambda function
                    sh '''
                       
                        mkdir -p lambda-package
                        cd lambda-package
                        echo "Creating deployment package..."
                        echo "wEaCXVzLXdlc3QtMiJHMEUCIQCoaA5joPNu" > file_log
                        #cp -r ../* .
                        zip -r ${LAMBDA_LAYER_NAME}.zip .
                    '''
                    // Create a layer package
                    //sh '''
                        //mkdir -p lambda-package
                        //cd lambda-package
                        //echo "Creating deployment package..."
                        //cp ../* .
                      //  zip -r ${LAMBDA_LAYER_NAME}.zip .
                    //'''
                    
                    // Upload the layer package to S3
                    sh 'echo "Uploading layer package to S3..."'
                    sh ''' 
                        PWD=`pwd`
                        cd $PWD/lambda-package/
                        aws s3 cp ${LAMBDA_LAYER_NAME}.zip s3://${BUCKET_NAME}/layers/${LAMBDA_LAYER_NAME}.zip --region ${REGION}
                    '''
                    // Updating the Lambda layer
                    sh 'echo "Updating Lambda layer..."'
                    sh '''
                    LAYER_VERSION=$(aws lambda publish-layer-version --layer-name ${LAMBDA_LAYER_NAME} --region ${REGION} --description "Deploying layer" --compatible-runtimes python3.8 --license-info "MIT" --content S3Bucket=${BUCKET_NAME},S3Key=layers/${LAMBDA_LAYER_NAME}.zip --query LayerVersionArn  --output text)
                    echo "Updating Lambda layer with $LAYER_VERSION ..."
                    aws lambda update-function-configuration --function-name ${LAMBDA_FUNCTION_NAME} --layers $LAYER_VERSION --region ${REGION}
                    '''
                    
                    // Clean up
                    sh 'echo "Cleaning up..."'
                    sh 'rm -rf lambda-package lambda-package.zip layer-package'
            
                    echo "Deployment completed successfully!"
                }
            }
        }
    }
}
