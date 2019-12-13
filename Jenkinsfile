#!/bin/env groovy

pipeline {
  agent none
  stages {
    stage('Build') {
      agent {
        docker {
          image 'maven:3.5.0'
        }
      }
      steps {
          sh 'mvn clean install -B'
      }
    }
    stage('Build container') {
      agent any
      steps {
        script {
            pom = readMavenPom file: 'pom.xml'
            TAG = pom.version
            sh "docker build -t petclinic:${TAG} ."
        }
      }
    }
    stage('Run local Container') {
           agent any
           steps {
               sh 'docker rm -f petclinic:${TAG} || true'
               sh "docker run -d --network=bridge --name petclinic osuide/petclinic:${TAG}"
           }
    }
}
}
