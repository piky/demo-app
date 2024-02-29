#!/usr/bin/env groovy
pipeline {
    agent any

    environment {
      registry = "piky/demo-app"
      registryCredential = 'dockerhub'
      dockerImage = ''
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
              script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
                docker.withRegistry( '', registryCredential ) {
                  dockerImage.push()
                }
              // sh "docker rmi $registry:$BUILD_NUMBER"
              }
            }
        }
    }
}
