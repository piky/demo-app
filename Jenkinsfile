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
                sh 'docker build -t $DOCKER_REGISTRY:$BUILD_NUMBER .'
            }
        }
    }
}
