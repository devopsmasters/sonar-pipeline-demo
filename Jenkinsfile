pipeline {
    agent any

    stages {
        stage('setup sonar container') {
            steps {
                sh 'docker run  -d -p 9000:9000 --name mysonar1 sonarqube:6.3.1'
                sh 'sleep 10'
            }
        }
        stage('install and sonar parallel') {
            steps {
                parallel(install: {
                    sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                }, sonar: {
                    sh "mvn sonar:sonar -Dsonar.host.url=http://127.0.0.1:9000"
                })
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                    sh 'docker stop mysonar1'
                }
            }
        }
    }
}
