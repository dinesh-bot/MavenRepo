pipeline {
  agent {
    docker {
      image 'node:10-alpine'
      args '-p 20001-20100:3000'
    }

  }
  stages {
    stage('Install Packages') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test and Build') {
      parallel {
        stage('Run Tests') {
          steps {
            sh 'npm run test'
          }
        }
        stage('Create Build Artifacts') {
          steps {
            sh 'npm run build'
          }
        }
      }
    }
    stage('Deployment') {
      parallel {
        stage('Staging') {
          when {
            branch 'staging'
          }
          steps {
            withAWS(region: '<us-east-1>', credentials: '<220da6e1-7bcc-4445-893c-9d0edbc9e6c2>') {
              s3Delete(bucket: '<sas-test-bucket4>', path: '**/*')
              s3Upload(bucket: '<sas-test-bucket4>', workingDir: 'build', includePathPattern: '**/*')
            }

            mail(subject: 'Staging Build', body: 'New Deployment to Staging', to: 'ShankyAnky03@gmail.com')
          }
        }
        stage('Production') {
          when {
            branch 'master'
          }
          steps {
            withAWS(region: '<us-east-1>', credentials: '<220da6e1-7bcc-4445-893c-9d0edbc9e6c2>') {
              s3Delete(bucket: '<sas-test-bucket4>', path: '**/*')
              s3Upload(bucket: '<sas-test-bucket4>', workingDir: 'build', includePathPattern: '**/*')
            }

            mail(subject: 'Production Build', body: 'New Deployment to Production', to: 'ShankyAnky03@gmail.com')
          }
        }
      }
    }
  }
  environment {
    CI = 'true'
    HOME = '.'
    npm_config_cache = 'npm-cache'
  }
}