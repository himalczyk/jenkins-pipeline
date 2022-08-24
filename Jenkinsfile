pipeline {
    agent any

    stages {
        
        stage("build") {
            steps {
                //commands executed on Jenkinsserver, execution of commands with Jenkinsagent
                // sh 'npm install'
                // sh 'npm build'
                echo 'building the application'
            }
        }
        
        stage("test") {
            steps {
                echo 'testing the application'
            }
        }

        stage("deploy") {
            steps {
                echo 'deploying the application'
            }
        }
    }
}

node {
    // groovy script
}