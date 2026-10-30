#!groovy

pipeline {
  agent none
  stages {
    stage('Maven Install') {
      agent {
        docker {
          image 'maven:3.9-eclipse-temurin-25' 
          reuseNode true
        }
      }
      steps {
        sh 'mvn clean install'
        stash name: 'petclinic-jar', includes: 'target/*.jar'
      }
    }
    stage('Docker Build') {
      agent any
      steps {
        unstash 'petclinic-jar'
        sh 'docker build -t spring-petclinic:gestion-udem-jenkins .'
      }
    }
  }
}
