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
      environment {
        TESTCONTAINERS_HOST_OVERRIDE = 'host.docker.internal'
      }
      steps {
        sh 'apt-get update -qq && apt-get install -y --no-install-recommends docker.io docker-compose-v2'
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
    stage('Docker Push') {
      agent any
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh 'printf "%s" "$dockerHubPassword" | docker login -u "$dockerHubUser" --password-stdin'
          sh 'docker tag spring-petclinic:gestion-udem-jenkins "$dockerHubUser/spring-petclinic:gestion-udem-jenkins"'
          sh 'docker push "$dockerHubUser/spring-petclinic:gestion-udem-jenkins"'
          sh 'docker logout'
        }
      }
    }
  }
}
