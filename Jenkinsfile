pipeline {
 agent any
  // using the Timestamper plugin we can add timestamps to the console log
  options {
    timestamps()
  }
  parameters{
     string(name: 'SLACK_ROOM',
               description: 'slack channel name',
               defaultValue: 'jenkins-training')
     
     string(name: 'EMAIL_ADDRESS',
               description: 'email address',
               defaultValue: 'talluri@netapp.com')
   string(name: 'VERSION',
               description: 'app version',
               defaultValue: '1.1')
    
  }
  environment {
    IMAGE = "sampleimage"
    VERSION = "${params.VERSION}"
    CHAT_ROOM = "${params.SLACK_ROOM}"
  }
  stages {
    stage("setup"){
      steps{
       script{
          log.info("I am info")
          log.info("I am warning")
          sh "mkdir -p arch"
          echo params.VERSION
          log_local_lib = load("helper.groovy")
          log_local_lib.info("Hello from local lib")
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
        expression {
            return env.VERSION != '1.1';
        }
      }
      steps {
       
        sh """
          echo "actual build "
        """
      }
    }
  }

  post {
      success{
           archiveArtifacts "arch/*"
           script{
                
          	slackChat.notifyPass(currentBuild, "Build Passed")
      	   }
      }
    failure {
      // notify users when the Pipeline fails
      mail to: params.EMAIL_ADRESS,
          subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
          body: "Something is wrong with ${env.BUILD_URL}"
     
    }
  }
}

