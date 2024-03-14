#!/usr/bin/env groovy

pipeline {

    environment {
      REPOSITORY = "https://github.com/piky/demo-app.git"
      REGISTRY = "piky/demo-app"
      DOCKERHUB_CREDENTIALS= credentials('dockerHub')
    }
    
    agent  {
        kubernetes {
            defaultContainer 'buildkitd'
            yamlFile 'k8s/agent-buildkit.yaml'
            retries 1
        }
    }
   
    stages {
      stage('SCM Get Code') {
        steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: "$REPOSITORY"]]])
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
            steps {
              script {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh "docker buildx build --push --tag $REGISTRY:$BUILD_NUMBER --build-context project=$REPOSITORY ."
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
    } //stages
    post {
      always {  
        sh 'docker logout'           
      }
    }
} // pipeline
