pipeline{
    agent any
    tools{
        jdk "JDK"
        maven "Apache Maven"
    }
    environment{
       APP_NAME='java-mvn-app'
       }
    parameters{
        choice(name:'version',choices:['1.1.0','1.2.0','1.3.0'],description:'version of the code')
        booleanParam(name:'executeTests',defaultValue:true,description:'tc validation')
    }
    stages{
           stage("COMPILE"){
               
               steps{
                   script{
                       echo "Compiling the code"
                       sh 'mvn compile'
                   }
               }
           }
           stage("UNITTEST"){
               
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
               
               steps{
                   script{
                       echo "Packaging the code"
                       sh 'mvn package'
                   }
               }
           }
           stage("BUILD THE DOCKER IMAGE"){
               
              
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
              
               steps{
                   script{
                       echo "Deploying the image on k8s"
                       sh 'envsubst < kubernetes/deploy.yml | sudo /usr/bin/kubectl apply -f deploy.yml'
                   }
               }
           }
       }
}
