---
layout: post
title: "Getting Started"
date: 2020-07-08 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Getting Started'
navi-order: 'a1-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: Docker is a container technique, which has much lower overhead than VM. Docker contains a lot of advantages lightweight, convenience, quickness, and portable. Image, Container, CLI, Registry.
excerpt: Docker is a container technique, which has much lower overhead than VM. Docker contains a lot of advantages lightweight, convenience, quickness, and portable. Image, Container, CLI, Registry.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1"%}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

<u>Docker is a container technique, which has much lower overhead than VM</u>. Docker contains a lot of advantages lightweight, convenience, quickness, and portable.

[Docker Inc][7] lunched the docker project during 2011. Docker debuted to the public in 2013, release it as an [open-source][10] project in May in the same year. In release 0.9, Docker writes its components by [go][8] language, instead of using the previous [LXC][9].

Docker can package your application and dependency into a container, which is an isolated environment from others. Containers are lightweight and didn't require an extra load of a hypervisor. You can launch multiple apps simultaneously in a container (**Don't recommend to run multiple apps in a single docker container, run them each in separate containers instead**). 

Containers are running on top of **Docker Engine**, which is a ***client-server*** infrastructure application that mainly involves **CLI**, **Rest API**, and **Docker daemon**.
* **Docker daemon**: A ***server-side*** technology, which is a type of a long-running program, manages underlying data, such as containers, apps, volumes, networks.

* **Rest API**: A set of interfaces, which defined between ***client-side*** and server-side, as a communication purpose.

* **CLI(aka. *Command Line Interface*)**: A client-side technology, which used to interact with users in a *[command-line window][4]*. CLI transfer commendations that users inputted into a **Rest API** format then send those APIs to the server(**Docker daemon**).

<div class="imgcenter" markdown="1">
![Alt][1]*Docker Engine, come from [docker.com][2]*
</div>

Docker CLI doesn't manage containers and other underlying resources directly. Docker CLI is only available to send REST APIs to the server. Those APIs instructs Docker Daemon to manage the underlying sources on the server-side. In most of the cases,  Docker Deamon and CLI installed in the same host. You can install Docker Deamon in a remote server, install CLI in local clients, configure clients related to the target docker daemon server.

<div class="imgcenter" markdown="1">
![Alt][3]*Multiple CLI clients related to one Daemon server*
</div>


**Docker Image** is a read-only template with instructions for creating a docker container. **Docker container** is a runnable instance of a docker Image. On the other hand, a Docker Image can exist without docker containers, whereas, a docker container depends on docker images and use them to build up a run-time environment. So their relationship is that <u>a docker container is an instance of a docker image</u>.

**Docker registry** store docker images. *Docker Hub* is a public registry that anyone can use, and Docker Engine looks for docker images on Docker Hub by default. You can even configure to run your own registry.

<div class="imgcenter" markdown="1">
![Alt][5]*Docker architecture, come from [docker.com][6]*
</div>

[1]: /public/img/2020-07-08-getting-started-a.png
[2]: https://docs.docker.com/get-started/overview/#the-docker-daemon
[3]: /public/img/2020-07-08-getting-started-b.png
[4]: https://en.wikipedia.org/wiki/Command-line_interface
[5]: /public/img/2020-07-08-getting-started-c.svg
[6]: https://docs.docker.com/get-started/overview/#docker-architecture
[7]: http://www.docker.com/
[8]: https://en.wikipedia.org/wiki/Go_(programming_language)
[9]: https://en.wikipedia.org/wiki/LXC
[10]: https://github.com/docker