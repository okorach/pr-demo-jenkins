pipeline {
  agent any
  stages {
    stage('SCM') {
      steps {
        git 'https://github.com/okorach/pr-demo-github.git'
      }
    }
    stage('SonarQube LTS analysis') {
      steps {
        step {
          withSonarQubeEnv('SQ LTS') {
            script {
              def scannerHome = tool 'SonarScanner';
              sh "${scannerHome}/bin/sonar-scanner"
            }
          }
        }
      }
    }
    stage("SonarQube LTS Quality Gate") {
      steps {
        step {
          timeout(time: 5, unit: 'MINUTES') {
            def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
            if (qg.status != 'OK') {
              echo "Quality gate failed: ${qg.status}, proceeding anyway"
            }
          }
        }
      }
    }
    stage('SonarQube LATEST analysis') {

      steps {
        step {
          withSonarQubeEnv('SQ Latest') {
            script {
              def scannerHome = tool 'SonarScanner';
              sh "${scannerHome}/bin/sonar-scanner"
            }
          }
        }
      }
    }
    stage("SonarQube LATEST Quality Gate") {
      steps {
        timeout(time: 5, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
          step {
            def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
            if (qg.status != 'OK') {
              echo "Quality gate failed: ${qg.status}, proceeding anyway"
            }
          }
        }
      }
    }
  }
}