#!/usr/bin/env groovy

import groovy.transform.Field

@Field
String DOCKER_USER_REF = 'b51bb93e-844f-409a-be1e-3ab7748c2e8b'
@Field
String SSH_ID_REF = 'ssh-credentials-id'
pipeline{
    agent any

    tools {
        dockerTool 'docker'
    }

    stages {
        stage('build and test'){
            steps{
                sh 'docker --version'
                sh 'ls -la'
                sh 'docker build -t hoangdat1612/lab2_create_docker_image:latest .'
            }
        }
        stage('Docker login an push docker images to docker hub'){
            steps{
                withBuildConfiguration{
                    sh "docker login -u ${repository_username} -p ${repository_password}"
                    sh 'docker push hoangdat1612/lab2_create_docker_image:latest'
                }
            }
        }
        stage('deploy'){
            steps{
                withBuildConfiguration {
                    sshagent(credentials: [SSH_ID_REF]) {
                        sh 'ssh root@103.82.135.237 "docker run --detach --name lab2 -p 1612:8000 hoangdat1612/lab2_create_docker_image:latest"'
                        // sh 'ssh root@ec2-18-142-231-213.ap-southeast-1.compute.amazonaws.com "docker run --detach --name lab2 -p 1612:8000 hoangdat1612/lab2_create_docker_image:latest"'
                    }
                }
            }
        }
    }
}

void withBuildConfiguration(Closure body) {
    withCredentials([usernamePassword(credentialsId: DOCKER_USER_REF, usernameVariable: 'repository_username', passwordVariable: 'repository_password')]) {
        body.call()
    }
}