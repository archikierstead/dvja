pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
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
  dependencyCheckPublisher failedTotalCritical: 99, failedTotalHigh: 99, failedTotalLow: 99, failedTotalMedium: 99, pattern: '', unstableTotalCritical: 99, unstableTotalHigh: 99, unstableTotalLow: 99, unstableTotalMedium: 99


}
}
    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://ako20-buildartifacts-1cjdvvoov5jmk/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
    stage('Scan for vulnerabilities') {
    steps {
        sh 'java -jar dvja-*.war && zap-cli quick-scan --self-contained --spider -r http://127.0.0.1 && zap-cli report -o zap-report.html -f html'
    }
}
  }
  post {
    always {
        archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
    }
}
}
