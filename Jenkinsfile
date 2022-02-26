pipeline {
  agent any
  stages {
    stage('Build') {
      post {
        failure {
          mail(subject: 'Build state', body: 'Build failed', to: 'id_bouloudene@esi.dz')
        }

        success {
          mail(subject: 'Build state', body: 'Build succeeded', to: 'id_bouloudene@esi.dz')
        }

      }
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        archiveArtifacts 'build/libs/*.jar'
        junit(testResults: 'build/test-results/test/*.xml', allowEmptyResults: true)
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'Post build', body: 'Jenkins notification', to: 'id_bouloudene@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              bat 'gradle sonarqube'
            }

          }
        }

        stage('Test Reporting') {
          steps {
            cucumber 'reports/*.json'
          }
        }

      }
    }

    stage('Deployment') {
      steps {
        bat 'gradle publish'
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(channel: '#jenkins_tp', message: 'Project is built newly and deployed', token: 'T02SMHWTMPT/B034Q8F677F/zdVSeg4njKh8TOpl3sE1TI32', baseUrl: 'https://hooks.slack.com/services/')
      }
    }

  }
}