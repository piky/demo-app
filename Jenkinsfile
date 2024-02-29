#!/usr/bin/env groovy

pipeline {
    agent {
        kubernetes {
            defaultContainer 'nodejs'
            yamlFile 'KubernetesPod.yaml'
            retries 3
        }
    }

    environment {
      registry = "piky/demo-app"
      registryCredential = 'dockerhub'
    }
            
    stages {
        stage('Unit test') {
            steps {
                sh 'npm install'
               // sh 'npm run test:unit'
            }
        }
//        stage('OWASP dependencies Check') {
//            steps {
//              dependencyCheck additionalArguments: ''' 
//                   -o './'
//                    -s './'
//                    -f 'ALL' 
//                    --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
//        
//               dependencyCheckPublisher pattern: 'dependency-check-report.xml' 
//            }
//        }
        stage('Build & Push Docker Image') {
            steps {
                step {
                    script {
                      dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    }
                  }
                step {
                    script {
                      docker.withRegistry( '', registryCredential ) {
                      dockerImage.push()
                      }
                  }
              }
          }
    }
}
