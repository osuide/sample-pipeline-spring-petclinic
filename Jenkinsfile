pipeline {
    agent none
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
                   sh 'mvn -s $MAVEN_SETTINGS clean deploy -DskipTests=true -B'
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
                   image 'sebp/sonar-runner'
                   args '-e SONAR_ACCOUNT_LOGIN -e SONAR_ACCOUNT_PASSWORD -e SONAR_DB_URL -e SONAR_DB_LOGIN -e SONAR_DB_PASSWORD --network=demo-deployment-pipeline_default --entrypoint=""'
               }
           }
           steps {
               sh '/opt/sonar-runner-2.4/bin/sonar-runner -X -Dsonar.login=d86dd98a8743eaaef9241a195d07eb1cfb9bb18c -Dsonar.jdbc.url=${SONAR_DB_URL} -Dsonar.jdbc.username=${SONAR_DB_LOGIN} -Dsonar.jdbc.password=${SONAR_DB_PASSWORD} -Dsonar.host.url=http://localhost:19000'
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
}ca