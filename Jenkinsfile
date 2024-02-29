#!/usr/bin/env groovy
pipeline {
    agent any

    environment {
      DOCKER_REGISTRY = "piky/demo-app"
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
              script {
                dockerImage = docker.build "$DOCKER_REGISTRY" + ":$BUILD_NUMBER"  
              }
            }
        }
    }
}
