pipeline {
    agent any 
    environment {
        PATH = "/usr/bin:$PATH"
        tag = "2.0"
        dockerHubUser="akshay451995"
        containerName="insure-me"
        httpPort="8081"
    }
    stages {
        stage("code clone"){
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/akshay451995/asi-insurance.git']])
            }
        }
        stage("Maven build"){
            steps {
                sh "mvn clean install"
            }
        }
        stage("Build Docker Image"){
            steps{
                sh "docker build -t ${dockerHubUser}/insure-me:${tag} ."
            }
        }
        stage("push image to dockerhub"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker push $dockerUser/$containerName:$tag"
}
            }
        }
        stage("Docker container deployment"){
            steps{
                sh "docker rm $containerName -f"
                sh "docker pull $dockerHubUser/$containerName:$tag"
                sh "docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag"
                echo "Application started on port: ${httpPort} (http)"
            }
        }
    }
}

