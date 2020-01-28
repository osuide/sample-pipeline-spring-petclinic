pipeline {
    agent any
    stages {
       stage('Build') {
           agent {
               docker {
                   image 'maven:3.5.0'
                   args '--network=demo-deployment-pipeline_default'
               }
           }
           steps {
               configFileProvider(
                       [configFile(fileId: 'nexus', variable: 'MAVEN_SETTINGS')]) {
                   sh 'mvn -s $MAVEN_SETTINGS clean deploy "-DskipTests=true" -B'
               }
           }
       }
       stage('Deploy to Tomcat') {
           agent {
               docker {
                   image 'alpine'
               }
           }
           steps {
               sh 'cp target/petclinic.war /usr/share/jenkins/ref/tomcat/petclinic.war'
           }
       }
       stage('Sonar') {
           agent  {
               docker {
                   image 'sonarsource/sonar-scanner-cli'
                   args '--network=demo-deployment-pipeline_default --entrypoint=""'
               }
           }
            steps {

                   sh '/opt/sonar-scanner/bin/sonar-scanner -X -Dsonar.projectKey=petclinic -Dsonar.login=7c8b33f31e0b98e462d3e9be57bee864e09db8f7 -Dsonar.host.url=http://sonar:9000 -Dsonar.java.binaries=/bin'
                 }
               }

        stage('Selenium') {
            agent {
                docker {
                    image 'liatrio/selenium-firefox'
                    args '--network=demo-deployment-pipeline_default'
                }
            }
            steps {
                sh 'ruby petclinic_spec.rb'
            }
        }
    }
}
