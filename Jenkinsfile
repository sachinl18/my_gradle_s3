pipeline {
    agent any

    environment {
        AWS_REGION = 'us-west-2'
        LAMBDA_FUNCTION_NAME = 'myLambda'
        JAR_FILE = 'build/libs/jb-hello-world-0.1.0.jar'
        S3_BUCKET = 'sachin-s3-lambda-bucket'
        S3_KEY = 'sachin-s3-lambda-bucket/jb-hello-world-0.1.0.jar'
    }

    stages {
        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }

        stage('Upload to S3') {
            steps {
                withAWS(credentials: 'aws-cred', region: "${env.AWS_REGION}") {
                    sh """
                    aws s3 cp ${env.WORKSPACE}/${env.JAR_FILE} s3://${env.S3_BUCKET}/${env.S3_KEY}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                withAWS(credentials: 'aws-cred', region: "${env.AWS_REGION}") {
                    sh """
                    aws lambda update-function-code \
                        --region ${env.AWS_REGION} \
                        --function-name ${env.LAMBDA_FUNCTION_NAME} \
                        --s3-bucket ${env.S3_BUCKET} \
                        --s3-key ${env.S3_KEY}
                    """
                }
            }
        }
    }
}
