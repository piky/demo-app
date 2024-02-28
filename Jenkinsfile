#!/usr/bin/env groovy

pipeline {
    agent {
        kubernetes {
            defaultContainer 'python3'
            yamlFile 'KubernetesPod.yaml'
            retries 2
        }
    }
            
    stages {
        stage('SCM Get Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/piky/demo-app.git']]])
            }
        }
    }
}
