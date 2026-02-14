pipeline {
  agent any
  tools {
    jdk 'jdk17'
    nodejs 'node16'
  }
  environment {
    SCANNER_HOME = tool 'sonarqube-scanner'
  }
  stages {
    stage('clean workspace') {
      steps {
        cleanWs()
      }
    }
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Joseph-peemi/Real-Terraform-Pipeline.git'
      }
    }
    stage('SonarQube Analysis') {
      steps {
        script {
          withSonarQubeEnv('SonarQube-Server') {
            withCredentials([string(credentialsId: 'SonarQube-Token', variable: 'SONAR_TOKEN')]) {
              sh '''
                $SCANNER_HOME/bin/sonar-scanner \
                -Dsonar.projectKey=nodejs-app \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://172.31.41.220:9000 \
                -Dsonar.token=$SONAR_TOKEN
              '''
            }
          }
        }
      }
    }
    stage('Quality Gate') {
      steps {
        timeout(time: 1, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-Token'
        }
      }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Trivy Scan') {
      steps {
        sh 'trivy fs . > trivy.txt'
      }
    }
  }
}
