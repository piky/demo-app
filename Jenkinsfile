#!/usr/bin/env groovy
pipeline {
    agent none // {
    //     kubernetes {
    //         defaultContainer 'nodejs'
    //         yamlFile 'KubernetesPod.yaml'
    //         retries 3
    //     }
    // }

    environment {
      repository = "https://github.com/piky/ts-example.git"
      registry = "piky/ts-example"
      registryCredential = 'dockerHub'
      dockerImage = ''
    }
            
    stages {
    //   stage('SCM Get Code') {
    //     steps {
    //       checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: "$repository"]]])
    //     }
    // }
    //     stage('Unit test') {
    //         steps {
    //             sh 'node --version'
    //             sh 'npm --version'
    //             sh 'npm install'
    //             // sh 'npm run test:unit'
    //         }
    //     }
    
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
        // stage ('Deploy to Kubernetes') {
        //     steps {
        //         script {
        //             withKubeConfig ([credentialsId: 'kubeconfig'])
        //             {
        //                 // sh 'curl -sLO https://raw.githubusercontent.com/StartloJ/ts-example/main/k8s/deployment.yaml'
        //                 // sh 'curl -sLO https://raw.githubusercontent.com/StartloJ/ts-example/main/k8s/service.yaml'
        //                 // sh """sed -i 's|dukecyber/ts-example:dev-v1.0|piky/ts-example:latest|' deployment.yaml"""
        //                 // sh """sed -i 's|8080|3000|' service.yaml"""
        //                 // sh 'kubectl apply -f service.yaml'
        //                 // sh 'kubectl apply -f deployment.yaml'
        //                 sh 'kubectl apply -f https://raw.githubusercontent.com/StartloJ/ts-example/main/k8s/deployment.yaml'
        //                 sleep(30)
        //                 sh 'kubectl get svc'
        //                 sh 'kubectl get pods'
        //             }
        //         }
        //     }
        // }
        stage ('Deploy to Kubernetes') {
            agent {
                kubernetes {
                    defaultContainer 'kubectl'
                    yamlFile 'agent-kubectl.yaml'
                    retries 1
                }
            }
            steps {
                withKubeConfig ([credentialsId: 'kubeconfig']) {
                    script {
                        sh 'kubectl apply -f k8s/'
                        sleep(30)
                        sh 'kubectl get svc'
                        sh 'kubectl get pods'
                    }
                }
            }
        }
    }
}
