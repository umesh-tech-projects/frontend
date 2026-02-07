pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET = 'jenkins-static-bd24df85'
        CLOUDFRONT_DISTRIBUTION_ID = 'E20C2D2T4JQ2XW'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/umesh-tech-projects/frontend.git'
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                  chmod +x build.sh
                  ./build.sh
                '''
            }
        }

        stage('Deploy to S3 & Invalidate CloudFront') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'frontend'
                ]]) {
                    sh '''
                      aws s3 sync build/ s3://$S3_BUCKET --delete
                      aws cloudfront create-invalidation \
                        --distribution-id $CLOUDFRONT_DISTRIBUTION_ID \
                        --paths "/*"
                    '''
                }
            }
        }
    }
}
