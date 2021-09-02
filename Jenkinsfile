pipeline {

  options { buildDiscarder(logRotator(daysToKeepStr: '7', numToKeepStr: '25')) }

  parameters {
    booleanParam(name: 'DEPLOY_ARTIFACT', defaultValue: false, description: 'Deploy Artifact')
    booleanParam(name: 'RUN_SONAR', defaultValue: false, description: 'Run Sonar Analysis')
  }

  agent { label 'builder' }

  stages {

    stage('Build Only') {
      when {
        branch 'PR-*'
      }
      steps {
        configFileProvider([configFile(fileId: 'MavenSettings', variable: 'MAVEN_SETTINGS_XML')]) {
          withMaven(){
            sh "mvn -s $MAVEN_SETTINGS_XML clean install"
          }
        }
      }
    }

    stage('Build & Deploy') {
      when {
        anyOf {
          branch 'main'
          expression { params.DEPLOY_ARTIFACT == true }
        }
      }
      steps {
        configFileProvider([configFile(fileId: 'MavenSettings', variable: 'MAVEN_SETTINGS_XML')]) {
          withMaven(){
            sh "mvn -s $MAVEN_SETTINGS_XML deploy"
          }
        }
      }
    }

    stage('Sonar') {
      when {
        expression { params.RUN_SONAR == true }
      }
      steps {
        configFileProvider([configFile(fileId: 'MavenSettings', variable: 'MAVEN_SETTINGS_XML')]) {
          withSonarQubeEnv(installationName: 'Sonar') {
            sh 'mvn -s $MAVEN_SETTINGS_XML sonar:sonar'
          }
        }
      }
    }

  }

}
