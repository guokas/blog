---
layout: post
title: "Docker Hub Automated Builds"
date: 2020-12-15 9:03:01+0800
categories:
  - Docker
tags:
  - DevOps
navi-enable-docker: true
navi-name: 'Docker Hub Automated Builds'
navi-order: 'a1-4-1-2'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: In this episode, I am going to show you how to implement a continuous integration mechanism with Docker Hub and Github. 
excerpt: Let's assume a scenario that if a team is developing a project, every team member responsible for their part of this project, and they will push their source code to the repository. As a project manager, you certainly don't want to rebuild and deploy the project manually over and over again. Here, the CI/CD is coming to play. 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1" 
        or post.navi-order == "a1-4"
        or post.navi-order == "a1-4-1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

Hi, Guys! I am back here. In this episode, I am going to show you how to implement a continuous integration mechanism with Docker Hub and Github. 

Let's assume a scenario that if a team is developing a project, developers responsible for their own part of this project, and they will push their source code to the repository. As a project manager, you certainly don't want to rebuild and deploy the project manually over and over again. Here, the CI/CD is coming to play. 

This article only focuses on continuous integration(CI) based on Github and Docker Hub.

# Sign in to your Github and Docker Hub account.
Sign in your [GitHub][1] and [Docker Hub][2], or register one if don't have them. I create a repository [docker-hub-CI-CD-explanation][18] and [docker-hub-automated-build-example][19] for demonstration in this article.
# Create a Github repository.
I created a Github repository named [Docker Hub CI CD Explanation][3] for next demonstration. 
And I created two branches, they are `main` and `dev` branches.
<div class="imgcenter" markdown="1">
![Alt][4]*main and dev branches*
</div>

The  `main` branch is for the production environment and `dev` branch is for the development environment. Developers push their code into the `dev` branch, and then the project manager merges the merge request of `dev` into the `main` branch once developer finished a comprehensive unit. 

As for this demonstration, I created a simple python common gateway interface(Python CGI) web application with simple file structures. If you are unfamiliar with the python CGI program, take look at the [Python Running A CGI Web Server With http.server Module][5].

CGI Web Example File Structures:
```
├─cgi-bin
│  └─hello.py
└─index.py
```

## Add Dockerfile
The `Dockerfile` will be used by Docker Hub when [Docker Hub Automatic Build Mechanism][6] builds your images. So the Dockerfile is the guide, which instructs Docker Hub that how to build your images. In this article, I create a `Dockerfile` on both `dev` and `main` branches. Currently, The file structures should look like this.

***file structures on branch: main***
```
├─Dockerfile
└─README.md
```

***file structures on branch: dev***
```
├─cgi-bin
│  └─hello.py
├─index.py
├─Dockerfile
└─README.md
```

As I will set two automatic builds on both `dev` and `main` branches in the later, so I need Dockerfile on each of them.  The `dev` branch is used for the development environment, once developers push their codes into `dev` branch, then the Docker Hub will automatically build up a development image.  The `main` branch is used for the production environment, once the project manager merges the request from the `dev` branch, then Docker Hub will automatically build up a production image.

# Create a Docker Hub repository and set automated builds
Sign in to your Docker Hub account. Navigate to *Account Settings* > *Linked Accounts*
<div class="imgcenter" markdown="1">
![Alt][7]*linked account on docker hub*
</div>
Currently, the docker hub supports linking accounts to [Github][14] and [Bitbucket][15].

When you finished linking accounts, then create a repository(*repositories* > *Create Repository*). 
I created a repository, which named *docker-hub-automated-build-example* for the demonstration.

<div class="imgcenter" markdown="1">
![Alt][8]*docker hub simple example*
</div>

Now, I need set up the Automated Builds, click the ***Builds***.
<div class="imgcenter" markdown="1">
![Alt][9]*docker hub automated builds example*
</div>

Connect to an external repository source, and choose your organization and selected a repository.
<div class="imgcenter" markdown="1">
![Alt][10]*docker hub automated builds configuration*
</div>
As you can see from the above configuration, I configured two Build Rules, which are for the `main` branch and the `dev` branch. The `main` branch uses the `latest` as its tag, and the `dev` branch uses the `dev-latest` as its tag.

You can also specify the tag as its source type, and then the automated builds will be triggered when new tags are created

Click the ***Create and Build*** button in the right bottom of this page. Then after few minutes, you will see your images.
<div class="imgcenter" markdown="1">
![Alt][11]*docker hub automated builds results*
</div>

# Test
The first time relatively takes more time, which really depends on the bandwidth and dependency size. In this example, both branches build images successfully. If your building failed, you can take a look at the detailed logs. for example:
<div class="imgcenter" markdown="1">
![Alt][12]*take a look at the recent builds*
</div>
<div class="imgcenter" markdown="1">
![Alt][13]*building logs*
</div>

Now, let us create a merge request on `dev` branch and merge this request on the `main` branch. When you finished the merge request, the Docker Hub will automatically retry to build a new image on the `main` branch.

Here, a simple synopsis of the leading steps in this article:
1. Add Dockerfile on Github or Bitbucket
2. Setup automatical build on Docker Hub



[1]: https://github.com/
[2]: https://hub.docker.com/
[3]: https://github.com/voltwu/docker-hub-CI-CD-explanation
[4]: /public/img/2020-12-15-docker-hub-automated-build-a.png
[5]: https://voltwu.github.io/blog/python/2019/10/08/Running-A-CGI-Web-Server-With-http-server-Module/
[6]: https://docs.docker.com/docker-hub/builds/
[7]: /public/img/2020-12-15-docker-hub-automated-build-b.png
[8]: /public/img/2020-12-15-docker-hub-automated-build-c.png
[9]: /public/img/2020-12-15-docker-hub-automated-build-d.png
[10]: /public/img/2020-12-15-docker-hub-automated-build-e.png
[11]: /public/img/2020-12-15-docker-hub-automated-build-f.png
[12]: /public/img/2020-12-15-docker-hub-automated-build-g.png
[13]: /public/img/2020-12-15-docker-hub-automated-build-h.png
[14]: http://github.com/
[15]: https://bitbucket.org/
[16]: https://github.com/voltwu/docker-hub-CI-CD-explanation
[17]: https://hub.docker.com/repository/docker/davidwxg/docker-hub-automated-build-example
[18]: https://github.com/voltwu/docker-hub-CI-CD-explanation
[19]: https://hub.docker.com/r/davidwxg/docker-hub-automated-build-example
