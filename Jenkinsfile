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

                   sh '/opt/sonar-scanner/bin/sonar-scanner -X -Dsonar.projectKey=petclinic -Dsonar.login=37f35578e7309a077c1df76b3828f339ca888089 -Dsonar.host.url=http://host.docker.internal:19000 -Dsonar.java.binaries=/bin'
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
