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
      registryCredential = 'dockerHub'
      dockerImage = ''
    }
            
    stages {
        stage('Unit test') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm install'
                // sh 'npm run test:unit'
            }
        }
    
        // stage('OWASP dependencies Check') {
        //     steps {
        //       dependencyCheck additionalArguments: ''' 
        //            -o './'
        //             -s './'
        //             -f 'ALL' 
        //             --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
        
        //        dependencyCheckPublisher pattern: 'dependency-check-report.xml' 
        //     }
        // }
        // stage('Build & Push Docker Image') {
        //     steps {
        //       script {
        //         dockerImage = docker.build registry + ":$BUILD_NUMBER"
        //         docker.withRegistry( '', registryCredential ) {
        //           dockerImage.push()
        //         }
        //         sh "docker rmi $registry:$BUILD_NUMBER"
        //       }
        //     }
        // }
    }
}
