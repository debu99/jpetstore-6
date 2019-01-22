pipeline {
  agent any
  stages {
    stage('Build') {
      parallel {
        stage('Build') {
          steps {
            echo 'Start build'
            sh 'mvn clean install -Dlicense.skip=true'
            sh 'mvn snoar:snoar -Dsonar.host.url=http://13.250.63.9:8080 -Dlicense.skip=true'
            echo 'Sonarqube test complete'
          }
        }
        stage('Print Tester Credentials') {
          steps {
            echo 'The tester is ${TESTER}'
            sleep 8
          }
        }
        stage('Print Build Number') {
          steps {
            echo 'This is Build Number ${BUILD_ID}'
            sleep 5
          }
        }
      }
    }
    stage('JFrog push') {
      steps {
        script {
          def server = Artifactory.server "artifactory"
          def buildInfo = Artifactory.newBuildInfo()
          def rtMaven = Artifactory.newMavenBuild()

          rtMaven.tool = 'maven'
          rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'

          buildInfo = rtMaven.run pom: 'pom.xml', goals: "clean install -Dlicense.skip=true"
          buildInfo.env.capture = true
          buildInfo.name = 'jpetstore-6'
          server.publishBuildInfo buildInfo
        }

      }
    }
    stage('Deploy Prompt') {
      steps {
        echo 'Starting deployment'
        input 'Confirm?'
        echo 'Deployment completed'
      }
    }
  }
  tools {
    maven 'maven'
  }
  environment {
    TESTER = 'test333'
  }
}