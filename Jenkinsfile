// We have not added AWS credentials to Jenkins
// def awsCredentials = [[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-personal']]

pipeline {
  agent any

  environment {
    HELLO_WORLD = "Hello world!"
  }

  options {
    disableConcurrentBuilds()
    parallelsAlwaysFailFast()
    // timestamps() // Apparently this is only available via a plugin: https://www.jenkins.io/doc/book/pipeline/syntax/#options
    // withCredentials(awsCredentials)
  }

  stages {
    stage('Build & Say Hello World') {
      parallel {
        stage('Build') {
          steps {
            sh 'npm run build'
          }
        }

        stage('Say Hello World') {
          steps {
            echo "${env.HELLO_WORLD_ENV_VAR}"
          }
        }
      }
    }

    stage('Deploy') {
      steps {
        sh 'cdk deploy --require-approval=never'
      }
    }

    stage('Destroy') {
      steps {
        sh 'cdk destroy --require-approval=never'
      }
    }

    stage('Trigger PROD Build') {
      when {
        branch 'master'
      }

      steps {
        build job: '/declarative-jenkins-PROD', wait: false
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}
