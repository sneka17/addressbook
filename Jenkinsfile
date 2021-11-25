pipeline{
    agent any
    tools{
        jdk 'myjava'
        maven 'mymaven'
    }
    parameters{
        choice(name:'VERSION',choices:['1.1.0','1.2.0','1.3.0'],description:'version of the code')
        booleanParam(name: 'executeTests',defaultValue: true,description:'tc validity')
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
                    params.executeTests == true
                }
            }
            steps{
                script{
                    echo "Testing the code"
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
        
        stage("DEPLOY"){
            
            
            steps{
                script{
                    echo "Deploying the app"
                    echo "Deploying version ${params.VERSION}"
                }
            }
    }
}
    }
