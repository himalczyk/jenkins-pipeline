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

## Where does 	Declarative: Checkout SCM come from

By defining git repository to be checked out, basically in the config UI, we declared the checkout there to happen, it automatically happens then in the pipeline

# Test changes in a Jenkinsfile in Jenkins UI

Go to one of the branches, check the history, click inside -> click replay ( allows to adjust the latest run and re-run it again), basically adjust the jenkinsfile

## For a groovy or script to be executed, it needs to be inclosed in script {}

```
script {

}
```

Testing a groovy execution

```
script {
    def test = 2 + 2 > 3 ? 'True' : 'Not True'
    echo test
}
```

Check the logs on the pipeline run on the branch


## Restart from Stage

On the runned history, click the arrow down and restart a stage configured (build, test, deploy)

## Trigger build on commit

### Methods

**Push notification**

More efficient, only on relevant change
Version control notifies Jenkins on new commit

### Push notification config

Source code management dependent

1. Install Jenkins plugin based on Version Control System
2. Configure Repository Server Hostname
3. Add Access Token or Credential

 Manage Jenkins -> Configure system -> Github/Gitlab (or another version control) add server -> advanced -> override hook url -> copy the url to the webhook of the git version control repository

 Same on git repo -> settings -> webhooks

 Copy the url from the above jenkins server webhook and paste

 Check the push events checbkox to trigger a push event to this url

 Develoepr -> Commit on Git -> push to Jenkins webhook url -> Change made, lets build

**Polling**

Jenkins polls in regular intervalls if there is any change

Inside of the jenkins inside of the pipeline

Scan Multibranch Pipeline Triggers -> Periodically if not otherwise run -> choose interval (e.g. usually 15min-1h)

Jenkins checks every 1 min if there was ane change on git <-> Git

Sometimes they are unreliable, blocked by jenkins firewall.

### Good strategy for checking polling / push notification

Good strategy to configure both as backup plan

Push notification on changes + polling each 1-2hours

# Configure build tools in Jenkins. Configure Gradle

## 4 main build tools

Backend: Maven, Gradle

Frontend: npm || yarn

At least one of this build tools is needed in one of the build configurations

## How to use these tools in Jenkins?

Some buold tools are already available

Some tools need to be installed as plugins

1. Check if already available? If yes, configure it

2. Install it from Plugins and do Step 1)

3. Use it in your build config (Jenkinsfile)

### Configuration

Manage Jenkins -> Global Tool Configuration

Each chosen tools can be installed in Jenkins inself in the configuration while adding and choosing the installation source

### Install some tool (npm or yarn)

Manage Jenkins -> Manage plugins

Search for plugin -> check box -> install

### Building an example simple Jenkinsfile with Gradle and NodeJS

The same way we can use npm and maven

```
pipeline {
    agent any
    stages {
        stage("run frontend") {
            steps {
                echo "executing yarn.."
                nodejs('Node-10.17') {
                    sh 'yarn install'
                }
            }
        }
        stage("run frontend") {
            steps {
                echo "executing gradle.."
                withGradle() {
                    sh './gradlew -v'
                }
            }
        }
    }
}
```

The same can be used with a tools **argument** in jenkinsfile instead of repr: 

REPR IS (the instanstation/calling of the tools withGradel(), nodejs('Node-10.17'))

```
withGradle() {
    sh './gradlew -v'
}
```

```
tools {
    gradle 'Gradle-6.2'
}
```