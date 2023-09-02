pipeline {
  agent any
  environment {
    DIGITALOCEAN_ACCESS_TOKEN = credentials('do-api-token')
  }
  stages {
    stage('Install') {
      steps {
        script {
          // Ensure that the doctl CLI is installed
          sh 'curl -sSL https://github.com/digitalocean/doctl/releases/latest/download/doctl-$(uname -s)-$(uname -m) -o /usr/local/bin/doctl'
          sh 'chmod +x /usr/local/bin/doctl'
        }
      }
    }
    stage('Connect') {
      steps {
        script {
          // Authenticate with DigitalOcean using your access token
          sh 'doctl auth init -t $DIGITALOCEAN_ACCESS_TOKEN'
        }
      }
    }
    stage('Deploy') {
      steps {
        script {
          // Navigate to your project directory and deploy
          dir('path/to/your/project') {
            sh 'doctl sls deploy .'
          }
        }
      }
    }
    stage('Get URL for hello') {
      steps {
        script {
          // Navigate to your project directory and get the URL for hello
          dir('path/to/your/project') {
            def url = sh(script:'doctl sls fn get sample/hello --url', returnStdout:true).trim()
            sh script: "curl $url?name=Jenkins", returnStatus: true, nohup: false
          }
        }
      }
    }
    stage('Get URL for goodbye') {
      steps {
        script {
          // Navigate to your project directory and get the URL for goodbye
          dir('path/to/your/project') {
            def url = sh(script:'doctl sls fn get sample/goodbye --url', returnStdout:true).trim()
            sh script: "curl $url?name=Jenkins", returnStatus: true, nohup: false
          }
        }
      }
    }
  }
  post {
    always {
      // Clean up: uninstall components and revoke access token
      sh 'doctl sls uninstall'
      sh 'doctl auth revoke'
    }
  }
}
