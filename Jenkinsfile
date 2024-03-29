@Field
String DOCKER_USER_REF = '<DOCKERHUB_ID_PLACEHOLDER>'
@Field
String SSH_ID_REF = '<SSH_ID_PLACEHOLDER>'

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
        /* groovylint-disable-next-line SpaceAfterClosingBrace */
        }stage('Docker login an push docker images to docker hub'){
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
                        ssh root@ec2-18-141-234-249.ap-southeast-1.compute.amazonaws.com 'docker run --detach --name lab2 -p 1612:8000 hoangdat1612/lab2_create_docker_image:latest'
                        ssh root@ec2-18-142-231-213.ap-southeast-1.compute.amazonaws.com 'docker run --detach --name lab2 -p 1612:8000 hoangdat1612/lab2_create_docker_image:latest'
                    }
                }
            }
        }
    }
}

void withBuildConfiguration(Closure body) {
    withCredentials([usernamePassword(credentialsId: DOCKER_USER_REF, usernameVariable: 'repository_username', passwordVariable: 'repository_password')]) {
        body()
    }
}