pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  post {
     always {
         archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
     }
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
      }
    }
    stage('Check dependencies') {
      steps {
      dependencyCheck additionalArguments: '', odcInstallation: 'Dependency-Check'
      dependencyCheckPublisher pattern: ''
      }
    }
    stage('Analysis') {
      steps {
        sh "mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd spotbugs:spotbugs"
      }
    }
    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://ako2020-cicd-buildartifacts-12is1sgcta5si/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
 }
}
