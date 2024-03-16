#!/usr/bin/env groovy

pipeline {

    environment {
      REPOSITORY = 'https://github.com/piky/demo-app.git'
      REGISTRY = 'piky/demo-app'
      DOCKERHUB_CREDENTIALS = credentials('dockerHub')
      MY_KUBECONFIG = credentials('kubeconfig')
      BUILDER = 'buildkitd'
      DEPLOYMENT = 'demo-webapp'
      NS = 'default'
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
                sh('node --version')
                sh('npm --version')
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
              withKubeConfig ([credentialsId: 'kubeconfig']) {
                script {
                  sh('docker buildx create --name=$BUILDER --driver=kubernetes --bootstrap --use')
                  sh('echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin')
                  sh('docker buildx build --build-context project=$REPOSITORY --tag $REGISTRY:build-$BUILD_NUMBER --push . ')
                  sh('docker buildx stop $BUILDER')
                  sh('docker buildx rm $BUILDER')
                }
              }
            }
        }

        stage ('Deploy to Kubernetes') {
             steps {
                 withKubeConfig ([credentialsId: 'kubeconfig']) {
                     script {
                         sh('kubectl --namespace $NS apply -f k8s/service.yaml -f k8s/deployment.yaml -f k8s/ingress.yaml')
                         sh('kubectl --namespace $NS set image deployment/$DEPLOYMENT $JOB_NAME=$REGISTRY:build-$BUILD_NUMBER')
                        //  sleep(300)
                         sh('kubectl --namespace $NS get svc')
                         sh('kubectl --namespace $NS get pods')
                     }
                 }
             }
         }
    } //stages
    post {
      always {  
        sh('docker logout')
      }
    }
} // pipeline
