#!/usr/bin/env groovy
pipeline {
    agent  {
        kubernetes {
            defaultContainer 'nodejs'
            yamlFile 'k8s/agent-nodejs.yaml'
            retries 1
        }
    }

    environment {
      repository = "https://github.com/piky/demo-app.git"
      registry = "piky/demo-app"
      registryCredential = 'dockerHub'
      dockerImage = ''
    }
            
    stages {
      stage('SCM Get Code') {
        steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: "$repository"]]])
        }
      }
      stage('Unit test') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                // sh 'npm install'
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

        stage('Build & Push Docker Image') {
            agent {
                kubernetes {
                    defaultContainer 'docker'
                    yamlFile 'k8s/agent-docker.yaml'
                    retries 1
                }
            }
            steps {
              script {
                sleep(300)
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
                // docker.withRegistry( '', registryCredential ) {
                //   dockerImage.push()
                // }
                // sh "docker rmi $registry:$BUILD_NUMBER"
              }
            }
        }

        // stage ('Deploy to Kubernetes') {
        //     agent {
        //         kubernetes {
        //             defaultContainer 'kubectl'
        //             yamlFile 'k8s/agent-kubectl.yaml'
        //             retries 1
        //         }
        //     }
        //     steps {
        //         withKubeConfig ([credentialsId: 'kubeconfig']) {
        //             script {
        //                 sh 'kubectl apply -f k8s/'
        //                 sleep(30)
        //                 sh 'kubectl get svc'
        //                 sh 'kubectl get pods'
        //             }
        //         }
        //     }
        // }
    }
}
