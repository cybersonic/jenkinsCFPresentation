pipeline {
  agent {
    docker {
      image 'ortussolutions/commandbox'
      args '-u=root'
    }

  }
  stages {
    stage('Build') {
      steps {
        echo 'In Build Script. '
        sh 'box install commandbox-cflint'
        sh 'box server start port=8080'
        sh 'box cfconfig import myConfig.json'
        sh 'box cfconfig import testConfig.json'
        sh 'box install'
        sh 'box server restart'
      }
    }
    stage('Unit Tests') {
      parallel {
        stage('Unit Tests') {
          steps {
            echo 'Running Unit Tests'
            sh 'box testbox run runner="http://localhost:8080/tests/runner.cfm" reporter="JUnit" directory="tests.bundles" outputFile="./junitResults.xml"'
            sh 'ls'
            junit 'junitResults.xml'
          }
        }
        stage('runCFLint') {
          steps {
            sh 'box cflint exitOnError=false --html pattern="**/src/**|**/tests/"'
            archiveArtifacts 'cflint-results.html'
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploying'
      }
    }
  }
}