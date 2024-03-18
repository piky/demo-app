#!/usr/bin/env groovy

// Here are sequential stages
pipeline {

  agent none

  stages {
    stage ('Provision agent pod') {
      agent {
        kubernetes {
          defaultContainer 'nodejs'
          yamlFile 'k8s/jenkins-agent-node.yaml'
          retries 1
        }
      }

      environment {
        REPOSITORY = 'https://github.com/piky/demo-app.git'
        REGISTRY = 'piky/demo-app'
        DOCKERHUB_CREDENTIALS = credentials('dockerHub')
        KUBECONFIG = credentials('kubeconfig')
        BUILDER = "buildkitd-${env.BUILD_NUMBER}-"
        DEPLOYMENT = 'demo-webapp'
        NS = 'default'
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
            sh 'npm install'
            sh 'npm run test:unit'
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
                sh('docker buildx create --name=$BUILDER --driver=kubernetes --bootstrap')
                sh('echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin')
                sh("docker buildx build --builder $BUILDER --build-context project=$REPOSITORY --tag $REGISTRY:build-${env.BUILD_NUMBER} --push . ")
                sh('docker buildx stop $BUILDER')
              }
          }
        }
        stage ('Deploy to Kubernetes') {
          steps {
                script {
                    sh('kubectl --namespace $NS apply -f k8s/service.yaml -f k8s/deployment.yaml -f k8s/ingress.yaml')
                    sh("kubectl --namespace $NS set image deployment/$DEPLOYMENT ${env.JOB_NAME}=$REGISTRY:build-${env.BUILD_NUMBER}")
                  //  sleep(30)
                    sh('kubectl --namespace $NS get svc')
                    sh('kubectl --namespace $NS get pods')
                }
          }
        }
      } // inner stages

      post {
        always {
          sh('docker buildx rm $BUILDER')
          sh('docker logout')
        }
      }
    } // stage
  } // outer stages

} // pipeline
