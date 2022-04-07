pipeline {
    agent any
        environment {
            AWS_DEFAULT_REGION="us-east-1"
        }

    stages {
        stage('Create storage stack') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-creds', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        aws cloudformation deploy --stack-name storage --template-file templates/s3-dynamo_stack.yaml --parameter-overrides file://templates/bucketname.json

                    '''
                }
            }
        }
        stage('Copy Lambda code') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-creds', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]){
                    sh '''
                        aws cloudformation wait stack-create-complete --stack-name storage
                        aws s3 cp templates/lambdapost.zip s3://lambda-apigw-bucket-paramf3f34g3
                        aws s3 cp templates/lambdaget.zip s3://lambda-apigw-bucket-paramf3f34g3
                    '''
                }
            }
        }
        stage('Create API Gateway and Lambda functions') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-creds', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]){
                    sh '''
                        aws cloudformation deploy --stack-name stack1 --template-file templates/api-lambda-stack2.yaml --capabilities CAPABILITY_IAM
                    '''
                }
            }
        }
    }
}

