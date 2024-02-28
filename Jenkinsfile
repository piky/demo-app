#!/usr/bin/env groovy

pipeline {
    agent {
        kubernetes {
            defaultContainer 'nodejs'
            yamlFile 'KubernetesPod.yaml'
            retries 3
        }
    }
            
    stages {
        stage('SCM Get Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/piky/demo-app.git']]])
            }
        }
        stage('Unit test') {
            steps {
                sh 'npm install'
              }
          }
    }
}
