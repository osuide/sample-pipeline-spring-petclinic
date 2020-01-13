pipeline {
    agent any
    stages {
       stage('Build') {
           agent {
               docker {
                   image 'maven:3.5.0'
                   args '--network=sf_vmshared_default'
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
                   args '--network=sf_vmshared_default --entrypoint=""'
               }
           }
            steps {

                   sh '/opt/sonar-scanner/bin/sonar-scanner -X -Dsonar.projectKey=petclinic -Dsonar.login=1a68b418420076d4f5e99d0debb3491c226acd45 -Dsonar.host.url=http://sonar:9000 -Dsonar.java.binaries=/bin'
                 }
               }

        stage('Selenium') {
            agent {
                docker {
                    image 'liatrio/selenium-firefox'
                    args '--network=sf_vmshared_default'
                }
            }
            steps {
                sh 'ruby petclinic_spec.rb'
            }
        }
    }
}
