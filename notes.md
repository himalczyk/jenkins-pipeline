# Jenkins set up

Get the docker image from docker hub:

https://hub.docker.com/r/jenkins/jenkins

Docs: https://github.com/jenkinsci/docker/blob/master/README.md

docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts

expose port -p 8080:8080 -> default runs on that port
expose port -p 50000:50000 port -> master and slave communication
-d -> Run in detached mode -> run container in background

-v jenkins_home:/var/jenkins_home Bind named volume -> persist data of jenkins

docker ps -> to get container id
docker logs <id> -> to get the password generated to initialize on localhost port assigned 8080

PWD: 7f1732811f614bdca7e5ce52ba6e0660


## Types of Jenkins Projects

### Freestyle

simple, single task

e.g. run tests

### Pipeline

whole delivery cycle

e.g. test | build | ..

for a single branch

### Multibranch pipeline

like pipeline, but for **multiple branches**

# Create multibranch pipeline with git

Create new item -> name it -> type: multibranch pipeline

## Configure branch sources

Copy paste the copy with https from clone icon from the repository

Behaviours -> filter branches by regular expression (java type) 

## Build configuration

From Jenkinsfile

# Configuration

## Credentials in Jenkins

### Scopes

System -> only available on jenkins server, NOT for jenkins jobs

Global -> Everywhere available

### Kind

Username & password
Certificate
Secret File
Secret text
SSH username with private key

You can get new types of credentials with new plugins

### ID

Reference for credentials, used in builds,  jobs, jenkinsfiles, pipeline etc, buil

### Credentials inside of the pipeline

Scopes -> only in multibranch pipeline

Global -> for overall
My-pipeline credentials -> system has not access to the pipeline, good for separation of credentials

## Discover branches by name

Regex: ^dev|master|feature.*$

dev OR master OR feature

'any character after feature **.**'
'any number of times *'
'**^** start of line'
'**$**end of line'

Include or exclude branches as you wish.

## Pipeline syntax | Jenkinsfile

### Scripted

First syntax

Groovy engine, script in groovy
advanced scripting capabalities, high flexibility

### Declarative

recent addition
easier to get started, but not that powerful
pre-defined structure

## Required fields of Jenkinsfile

```
pipeline {
    agent any

    stages {
        
        stage("build") {
            steps {

            }
        }
    }
}
```

- **pipeline** argument must be top-level
- **agent** where to execute

```
node {
    // groovy script
}
```

These two are equivalent to **node**

**stages** where the work happens
    - stages and steps

## Valid test jenkins file

```
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
```