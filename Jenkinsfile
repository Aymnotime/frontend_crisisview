pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    SONARQUBE_SERVER = 'sonarqube'
    SONAR_SCANNER = 'SonarScanner'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install') {
      steps {
        dir('.') { sh 'npm ci' }
      }
    }

    stage('Lint') {
      steps {
        dir('.') { sh 'npm run lint' }
      }
    }

    stage('Build') {
      steps {
        dir('.') { sh 'npm run build' }
      }
    }

    stage('Sonar') {
      steps {
        script {
          def scannerHome = tool(env.SONAR_SCANNER)
          withSonarQubeEnv(env.SONARQUBE_SERVER) {
            sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: false
        }
      }
    }
  }
}
