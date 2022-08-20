---
layout: post
title: "Jenkins with asp.net core CI&CD pipeline for Linux"
date: 2020-12-30 09:03:01+0800
categories:
  - Jenkins
tags:
  - DevOps
navi-enable-jenkins: true
navi-name: 'Jenkins with asp.net core CI&CD pipeline for Linux'
navi-order: a1-2
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: guild you to set up a CI and CD workflow pipeline step by step
excerpt: guild you to set up a CI and CD workflow pipeline step by step
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-jenkins %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

In this tutorial, I will guild you to set up a CI and CD workflow pipeline step by step. I use this GitHub repository [https://github.com/voltwu/Jenkins-.NET-Core-CI-CD-pipeline][17] to demonstrate this example, which has two branches, `main` and `dev`.

<div class="imgcenter" markdown="1">
![Alt][18]*Example repository*
</div>

The purpose of this article:
<div class="imgcenter" markdown="1">
![Alt][1]*Jenkins pipeline architecture*
</div>

Developers push their codes into a repository(Such as Github), Jenkins will automatically pull codes from that repository, then process build, test, publish and deploy. All of these will be processed automatically in the Jenkins pipeline.

# Prerequisites
1. A Linux machine. I am using Debian 9.
2. .Net Core 3.1 installed. Check [Net Core Install on Linux][3] for the installation guild.
3. A running Jenkins. Check [Jenkins Installation][2], if you haven't installed Jenkins yet. 

# Create A Job
Click *New Item*
<div class="imgcenter" markdown="1">
![Alt][4]*Create A New Job*
</div>

Fill item name and click **Multibranch Pipeline**. The other two types ***Freestyle project*** and ***Pipeline*** are also commonly used in Jenkins. In contrast to the **Multibranch Pipeline**, the other two are function limited and feature lesser. The following is a brief comparison of three of them.

* **Freestyle Project**: Build or run simple jobs like allowing to specify the version control system from which you need to extract code and build it and call tests if available.
* **Pipeline**: Setup continuous delivery pipeline or say define the deployment pipeline as code, which allows you to orchestrate all the stages in delivering an application as one single pipeline project.
* **Multibranch Pipeline**: This is similar to Pipeline. Multibranch Pipeline not just contains all functionalities that Pipeline supported, more further, Multibranch Pipeline allows you to pick a specific branch. If you have two branches `dev` and `main`, `dev` for development, `main` for production, then you need to distinguish branches, so you can set up different pipelines.

## Branch Sources

<div class="imgcenter" markdown="1">
![Alt][5]*Branch Sources*
</div>

I use **Git** as the branch source example. Then fill in the ***project repository*** address, if the project is **private**, then you need to **add credentials**. This repository for this example is open, so I don't need to add any credentials.
<div class="imgcenter" markdown="1">
![Alt][6]*Git Repository*
</div>

The **Behaviours** describes how to identify what branches you want to use. Here I choose to discover all branches and then filter branch name using ***wildcards***.
<div class="imgcenter" markdown="1">
![Alt][7]*Filter name by wildcards*
</div>

<div class="imgcenter" markdown="1">
![Alt][8]*pick up all branches that starts with dev*
</div>

I filled in *Include* with `dev*`, which will pick up all branches that name start with *dev*.

## Build Configuration

<div class="imgcenter" markdown="1">
![Alt][9]*Build Configuration with jenkinsfile*
</div>

Choose Build Configuration Mode as by **Jenkinsfile**. 	`Script Path` specify the Jenkinsfile location, which will be located at the project's root path by default. If your Jenkinsfile located at another place, you can specify it at `Script Path`.

## Scan Multibranch Pipeline Triggers

<div class="imgcenter" markdown="1">
![Alt][10]*Periodically if not otherwise run*
</div>

Sometimes, the Jenkins may unexpectedly unresponsive to your code's push action. You should specify a periodical running task with an interval time, just in case of your Jenkins unresponsive.

## Create a Jenkinsfile

The following is my Jenkins file, which includes six stages. And in the later, when everything set up completely. You can visualize these stages in the UI component.

I won't discuss the pipeline syntax, as it already out of the scope of this article. Check this [Pipeline Handlebook][11] for scripted pipeline syntax and pipeline declarative syntax.
```
pipeline {
    agent any
    triggers {
        githubPush()
    }
    stages {
        stage('Restore packages'){
           steps{
               sh 'dotnet restore WebApplication.sln'
            }
         }
        stage('Clean'){
           steps{
               sh 'dotnet clean WebApplication.sln --configuration Release'
            }
         }
        stage('Build'){
           steps{
               sh 'dotnet build WebApplication.sln --configuration Release --no-restore'
            }
         }
        stage('Test: Unit Test'){
           steps {
                sh 'dotnet test XUnitTestProject/XUnitTestProject.csproj --configuration Release --no-restore'
             }
          }
        stage('Publish'){
             steps{
               sh 'dotnet publish WebApplication/WebApplication.csproj --configuration Release --no-restore'
             }
        }
        stage('Deploy'){
             steps{
               sh '''for pid in $(lsof -t -i:9090); do
                       kill -9 $pid
               done'''
               sh 'cd WebApplication/bin/Release/netcoreapp3.1/publish/'
               sh 'nohup dotnet WebApplication.dll --urls="http://104.128.91.189:9090" --ip="104.128.91.189" --port=9090 --no-restore > /dev/null 2>&1 &'
             }
        }
    }
}
```
As you can see from the above pipeline file., I set 6 stages, each stage corresponding different task.

# Setup automatic build on push 

In this section, I will set up an automatic build on push. Each push to the source code will trigger your Jenkins to run all of these stages as I described above. 

In order to complete that, we need to set on both Jenkins and Github, so they can communicate with others. Set webhook on Github. (Your Jenkins needs to run on a public server, So Github can notify your Jenkins server.)
<div class="imgcenter" markdown="1">
![Alt][12]*Github weebhook*
</div>

Fill in your webhook address as [http://server:port/github-webhook/][13].

In order to notify the Jenkins, you also need to add Following section at your *Jenkinsfile*:
<div class="imgcenter" markdown="1">
![Alt][14]*Jenkins notify as pipeline code*
</div>
Take a look at [How to trigger a Jenkins multibranch pipeline when code change is pushed to GitHub][16] for more details.


Now, Github will notify your Jenkins server, when new pushes happened in your Jenkins server.

# Test
Now, we have everything set up. 
Push somes codes into repository and take a look at stages.
<div class="imgcenter" markdown="1">
![Alt][15]*Jenkins notify as pipeline code*
</div>

Now, you have an entire workflow pipeline set up. Let's enjoy your Jenkins. If you have any questions or doubts, please feel free to leave comments below.

[1]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-a.png
[2]: /jenkins/2020/12/27/jenkins-installation/
[3]: https://docs.microsoft.com/en-us/dotnet/core/install/linux
[4]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-b.png
[5]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-c.png
[6]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-d.png
[7]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-e.png
[8]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-f.png
[9]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-g.png
[10]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-h.png
[11]: https://www.jenkins.io/doc/book/pipeline/
[12]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-i.png
[13]: http://server:port/github-webhook/
[14]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-j.png
[15]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-k.png
[16]: https://stackoverflow.com/questions/42062481/how-to-trigger-a-jenkins-multibranch-pipeline-when-code-change-is-pushed-to-gith?noredirect=1&lq=1
[17]: https://github.com/voltwu/Jenkins-.NET-Core-CI-CD-pipeline/tree/dev
[18]: /public/img/2020-12-30-Jenkins-with-asp-net-core-CI-CD-pipeline-for-linux-l.png
