pipeline {
  agent {
    docker {
      image 'bitwiseman/training-blueocean-sample'
      args '-u root -v $HOME/.m2:/root/.m2'
    }
    
  }
  stages {
    stage('Build') {
      steps {
        sh './jenkins/build.sh'
        archiveArtifacts 'target/*.war'
      }
    }
    stage('Test') {
      steps {
        parallel(
          "Frontend": {
            sh './jenkins/test-frontend.sh'
            junit '**/test-results/karma/*.xml'
            
          },
          "Backend": {
            sh './jenkins/test-backend.sh'
            
          },
          "Static": {
            sh './jenkins/test-static.sh'
            
          },
          "Performance": {
            sh './jenkins/test-performance.sh'
            
          }
        )
      }
    }
    stage('Deploy to Dev') {
      steps {
        parallel(
          "Deploy to Dev": {
            sh './jenkins/deploy.sh dev'
            
          },
          "Deploy to Staging": {
            input(message: 'Deploy to staging?', ok: '\'Fire away!\'', id: 'stg')
            sh './jenkins/deploy.sh staging'
            sh 'echo Notifying appropriate team members!'
            
          }
        )
      }
    }
  }
}