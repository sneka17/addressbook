pipeline {
    options {
        timeout(time: 1, unit: 'HOURS')
    }
    agent {
        label 'ubuntu-1804 && amd64 && docker'
    }
    stages {
        stage('build and push') {
            when {
                branch 'master'
            }
            sh "docker build -t docker/getting-started ."

            steps {
                withDockerRegistry([url: "", credentialsId: "dockerbuildbot-index.docker.io"]) {
                    sh("docker push docker/getting-started")
                }
            }
        }
    }
}


pipeline{
    agent none
    tools{
        jdk "JDK"
        maven "Apache Maven"
    }
    parameters{
        choice(name:'version',choices:['1.1.0','1.2.0','1.3.0'],description:'version of the code')
        booleanParam(name:'executeTests',defaultValue:true,description:'tc validation')
    }
    stages{
           stage("COMPILE"){
               agent {label 'ec2_slave3'}
               steps{
                   script{
                       echo "Compiling the code"
                       sh 'mvn compile'
                   }
               }
           }
           stage("UNITTEST"){
               agent {label 'ec2_slave3'}
               when{
                   expression{
                       executeTests == true
                   }
               }
               steps{
                   script{
                       echo "testing the code"
                       sh 'mvn test'
                   }
               }
               post{
                   always{
                       junit 'target/surefire-reports/*.xml'
                   }
               }
           }
           stage("PACKAGE"){
               agent {label 'ec2_slave3'}
               steps{
                   script{
                       echo "Packaging the code"
                       sh 'mvn package'
                   }
               }
           }
           stage("BUILD THE DOCKER IMAGE"){
               agent any
               when{
                   expression{
                       BRANCH_NAME == 'master'
                   }
               }
               steps{
                   script{
                       echo "Building the docker image"
                       echo "Deploying version is $version"
                       withCredentials([usernamePassword(credentialsId: 'docker hub credentials', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                       sh 'sudo docker build -t sneka/addressbook:$BUILD_NUMBER .'
                       sh 'sudo docker login -u $USERNAME -p $PASSWORD'
                       sh 'sudo docker push sneka/addressbook:$BUILD_NUMBER'
                       }
                   }
               }
           }
           stage("DEPLOY"){
               agent any
               when{
                   expression{
                       BRANCH_NAME == 'master'
                   }
               }
               steps{
                   script{
                       echo "Deploying the code"
                       echo "Deploying version is $version"
                   }
               }
           }
       }
}
