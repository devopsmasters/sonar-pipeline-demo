pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
        stage('setup sonar container') {
            steps {
                sh 'docker run  -p 9000:9000 --name mysonar sonarqube:6.3.1 -d'
            }
        }
        stage('install and sonar parallel') {
            steps {
                parallel(install: {
                    sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                }, sonar: {
                    sh "mvn sonar:sonar -Dsonar.host.url=http://localhost:9000/"
                })
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                    sh 'docker stop mysonar'
                }
            }
        }
    }
}
