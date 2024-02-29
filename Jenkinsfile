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
              withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')])
              {
                sh 'docker build -t $DOCKER_REGISTRY:$BUILD_NUMBER .'
              }
              {
                sh 'docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}'
                sh 'docker push $DOCKER_REGISTRY:$BUILD_NUMBER'
              }
              {
                sh 'docker rmi $DOCKER_REGISTRY:$BUILD_NUMBER'
              }
            }
        }
    }
}
