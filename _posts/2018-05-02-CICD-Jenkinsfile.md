---
layout: post
title: "CI环境搭建"
categories:
  - CICD
---
## CI框架
Jenkins+Gerrit+GIT
### Jenkins与Gerrit的集成
在Gerrit中的配置:  
1.Create the profile through in Gerrit web interface for your Jenkins user, and set up a SSH key for that user.  
2.Gerrit web interface > Admin > Groups > Non-Interactive Users > Add your jenkins user.  
3.Admin > Projects > ... > Access > Edit  
    Reference: refs/*  
    Read: ALLOW for Non-Interactive Users  
    Reference: refs/heads/*  
    Label Code-Review: -1, +1 for Non-Interactive Users  
    Label Verified: -1, +1 for Non-Interactive Users  

IMPORTANT: On Gerrit 2.7+, you also need to grant "Stream Events" capability. Without this, the plugin will not work, will try to connect to Gerrit repeatedly, and will eventually cause OutOfMemoryError on Gerrit.  

1.Gerrit web interface > People > Create New Group : "Event Streaming Users". Add your jenkins user.  
2.Admin > Projects > All-Projects > Access > Edit  
    Global Capabilities  
    Stream Events: ALLOW for Event Streaming Users  
在Jenkins中的配置：  
在Jenkins中的Manage Jenkins-->Plugin Manager中添加Gerrit Trigger插件  
插件安装完后在Manage Jenkins-->Gerrit Trigger中进行设置：  
![Gerrit Trigger设置](https://wiki.jenkins.io/download/attachments/45481993/server-settings.png?version=1&modificationDate=1278331124000&api=v2)

### Jenkinsfile编写
```groovy
#!groovy
pipeline {
    agent any
    environment {
        DOCKER_IMAGE = sh returnStdout:true, script: "cat Buildfile.yaml|grep docker-image|awk '{printf \$2}'"
        VERSION = sh returnStdout:true, script: 'cat Buildfile.yaml|grep version|awk \'{print $2}\''
    }
    stages {
        stage('unitTest: pep8') {
            steps {
                sh 'tox -e pep8'
            }
        }
        stage('unitTest: py27') {
            steps {
                sh 'tox -e py27'
            }
        }
        stage('Building') {
            when {
                beforeAgent true
                anyOf {
                    changelog 'RPM BUILD'
                    changelog 'DEPLOY'
                }
            }
            agent{
               docker {
                   image "ironic-p"
               }
           }
           steps {
               echo 'Building......'
                sh "git checkout $VERSION"
                sh 'python setup.py sdist'
                sh 'cp dist/*.tar.gz /root/rpmbuild/SOURCES/'
                sh "sed -i \"s/Release:\\([ \\t]*\\)\\([0-9]*\\)\\(.*\$\\)/Release:\\1\${BUILD_ID}\\3/g\" /root/rpmbuild/SPECS/openstack-ironic.spec"
                sh 'rpmbuild -ba /root/rpmbuild/SPECS/openstack-ironic.spec'
                sh "ls -l /root/rpmbuild/RPMS"
                sh "pwd"
                sh "ls"
                sh "cat Buildfile.yaml"
            }
       }
       stage('Deploy') {
            when {
                beforeAgent true
                changelog 'DEPLOY'
            }
           steps {
               echo 'Deploying......'
               ansiblePlaybook disableHostKeyChecking: true, playbook: 'test-deploy.yaml'

           }
       }
    }
}
```
