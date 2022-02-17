pipeline {
 agent any
  // using the Timestamper plugin we can add timestamps to the console log
  options {
    timestamps()
  }
  environment {
    IMAGE = "sampleimage"
    VERSION = "1.2"
    CHAT_ROOM="jenkins-training"
  }
  stages {
    stage("setup"){
      steps{
       script{
          log.info("message from shared lib")
          log.warning("shared lib warning")
          sh "mkdir -p arch"
          //load local lib
          log_from_lib = load("helper.groovy")
          log_from_lib.info("Hello from local library")
       }
      }
    }
    stage('Build') {
      steps {
          sh """
            echo "first stage"
            echo "sleeping for 20 sec"
            sleep 20
            echo "artifact1" > arch/result
          """   
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
   success {
        
          archiveArtifacts(artifacts: 'arch/*', allowEmptyArchive: true)
          slackChat.notifyPass(currentBuild, "Build Passed")         
      }
    failure {
      // notify users when the Pipeline fails
      mail to: 'talluri@netapp.com',
          subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
          body: "Something is wrong with ${env.BUILD_URL}"
    }
  }
}
