#!/usr/bin/env groovy

pipeline {
    agent {
        kubernetes {
            defaultContainer 'nodejs'
            yamlFile 'KubernetesPod.yaml'
            retries 3
        }
    }
            
    stages {
        stage('Unit test') {
            steps {
                sh 'npm install'
               // sh 'npm run test:unit'
            }
        }
        stage('OWASP dependencies Check') {
            steps {
                scripts {
                    sh "npm audit --json > npm_audit_report.json"
                    // Check the report and fail the build if vulnerabilities are found
                    sh 'grep -q \'"vulnerabilities":\{"found":0\' npm_audit_report.json || (echo "Vulnerabilities found" && exit 1)'
                }
            }
        }
    }
}
