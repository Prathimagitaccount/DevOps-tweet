def registry = 'https://sanjo1997.jfrog.io/'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.8.8/bin:$PATH"
}
    stages {
        stage("build"){
            steps {
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build completed ----------"
            }
        }
        stage("test"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                 echo "----------- unit test Completed ----------"
            }
        }

    stage('SonarQube analysis') {
    environment {
      scannerHome = tool 'sanjo-sonar-scanner'
    }
    steps{
    withSonarQubeEnv('sanjo-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
      sh "${scannerHome}/bin/sonar-scanner"
    }
    }
  }
  stage("Quality Gate"){
    steps {
        script {
        sleep(60)
        timeout(time: 1, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
}
    }
  }
