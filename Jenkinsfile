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
           agent none
            steps {
            Script(
                   [docker.image('osuide/sonar-scanner').inside('-v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""').run]) {
                   sh '/opt/sonar-runner-2.4/bin/sonar-runner -Dsonar.projectKey=PetClinic  -Dsonar.projectKey=PetClinic -Dsonar.login=d86dd98a8743eaaef9241a195d07eb1cfb9bb18c -Dsonar.host.url=http://172.19.0.5:19000' 
                 }
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