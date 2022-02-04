pipeline {


 agent any

  // using the Timestamper plugin we can add timestamps to the console log
  options {
    timestamps()
  }

  environment {

    IMAGE = "sampleimage"
    VERSION = "1.2"
  }

  stages {
    stage{
      steps{
        script
          sh "mkdir arch"
        }
      
    }
    stage('Build') {
      
      steps {

    
          sh """
            echo "first stage"
            echo "artifact1" > arch
          """
        
        
      }
      post {
        success {
        
          archiveArtifacts(artifacts: 'arch/*', allowEmptyArchive: true)
        }
      }
    }

    stage('Quality Analysis') {
      parallel {
       
        stage ('parallel stage 1') {
          agent any  //run this stage on any available agent
          steps {
            echo 'Run integration tests here...'
          }
        }
        stage('parallel stage 2') {
          agent any
          
          environment {
            //use 'sonar' credentials scoped only to this stage
            SONAR = "sonar cred"//credentials('sonar')
          }
          steps {
            sh ' echo $SONAR_PSW'
          }
        }
      }
    }

    stage('Build and Publish Image') {
      when {
        branch 'master'  //only run these steps on the master branch
      }
      steps {
       
        sh """
          echo "actual build "
        """
      }
    }
  }

  post {
    failure {
      // notify users when the Pipeline fails
      mail to: 'team@example.com',
          subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
          body: "Something is wrong with ${env.BUILD_URL}"
    }
  }
}
