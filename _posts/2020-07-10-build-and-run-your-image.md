---
layout: post
title: "Build and run your image"
date: 2020-07-08 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Build and run your image'
navi-order: 'a1-2-1'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: Docker is a container technique. You can run Iof, IOS, Android, and OS, etc. In this episode, I am going to dive into containerizing a web application. You can clone this project's source code on Github. This source code is simple, only contains a few HTML static files and a Dockerfile.
excerpt: Docker is a container technique. You can run Iof, IOS, Android, and OS, etc. In this episode, I am going to dive into containerizing a web application. You can clone this project's source code on Github. This source code is simple, only contains a few HTML static files and a Dockerfile.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1" or
              post.navi-order == "a1-2" 
        %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

# Introduction

Docker is a container technique. You can run Iof, IOS, Android, and OS, etc. In this episode, I am going to dive into containerizing a web application. You can clone this project's source code on Github. This source code is simple, only contains a few HTML static files and a Dockerfile.

The most basic way to containerizing an App is the following steps:
1. Build your image with a docker file
2. Run your image as a container
3. Share your image

This article will explain to you almost everything you need to know while building your images.  

You can clone this project's source code down with git command.

<script src="https://gist.github.com/voltwu/d0e2509259153461551d0988ade35a2d.js"></script>

# Dockerfile
In most of the cases, different containers(windows, Linux) need to match different Dockerfiles. So if you have an app that needs to deploy on both Windows container and Linux container, then you may need to write different Dockerfiles for Windows and Linux. 

As the project's Dockerfile is for Linux Container, so let's analyze the file first.
## Linux

The Dockerfile in the Linux_tweet_app looks like this:

<script src="https://gist.github.com/voltwu/b4e2322fe24c4b25ac8de7259443c0f7.js"></script>

From the pre-exising `nginx:latest` image. This is an official image, for more information check [Docker Hub Nginx][1].

This `COPY` command is to copy from the **host file** to the **docker container**. The command pattern is `host_file container_file`. The above command copied the `index.html` file and the `linux.png` file into a docker container and put them under the `/usr/share/nginx/` folder.

The `EXPOSE` command instructs the docker to listen on specific ports. `EXPOSE 80 443` makes Docker container listen on 80, 443 port.

The last `CMD` command specifies the run command within the container.

## Windows
Linux and Windows are two completely different operating systems, and hence you can't use the above Dockerfile.

By the way, `nginx` also doesn't have an image for windows in [Docker Hub][1] now, and thus it will be a little complicated of working `nginx` in Windows container.

Replace the above Dockerfile with the following contents:

<script src="https://gist.github.com/voltwu/e87572144c229e3dcaaa90e953fa8cee.js"></script>

The above Dockerfile did 4 steps as bellow.
1. Use Windows image to build a Windows instance.
2. Set up an Nginx server inside Windows container.
3. Copy everything you need to the Windows container, and put them under the Nginx.
4. Specify the running command.

The semantic is almost the same as the Linux's, except that Nginx doesn't have a Windows image now.  The syntax is different, you need to note the `file path`, and using `PowerShell` in windows container. For more information about PowerShell command, see [PowerShell][3].

# Build your Image
You can get an image from either building a Dockerfile, or pulling from a registry. A Dockerfile produces an image, which consists of a set of layers.

The following quote explains the mechanism and advantages of the [Union File System][5].

<blockquote class="quote">
<p>
Each image consists of a series of layers. Docker makes use of union file systems to combine these layers into a single image. Union file systems allow files and directories of separate file systems, known as branches, to be transparently overlaid, forming a single coherent file system.
</p>
<p>
Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.
</p>
</blockquote>


Make sure you are in the folder that contains the Dockerfile, Use the following command to build your image:

<script src="https://gist.github.com/voltwu/f4b8bb5a94634f03b067dd9a336fdc0b.js"></script>

`--tag` or `-t` specifies that a name and optionally a tag in the `name:tag` format. dot '.' specifies the path is the current directory.

As the above mentioned that building an image depends on a Dockerfile, So what is the path rule of Dockerfile. You can specify the Dockerfile path explicitly or use the default path. Use the `-f` or `--file` to specify the name of the Dockerfile, the default is `PATH/Dockerfile`.

<script src="https://gist.github.com/voltwu/de8f75ac7c30a50d2f095301e0d9b4c7.js"></script>

# Run your Image as a Container
You can run your image as a container. Use the following command to start a container.

<script src="https://gist.github.com/voltwu/6ff4be245372ded7983c2cc56bf59af6.js"></script>

The above code will start a container with the image `tweet_app:2.0`.
* `--publish`/`-p`: Publish a container's port(s) to the host. The `--publish 8080:80` asks Docker to forward traffic incoming on the host’s port 8080 to the container’s port 80. If you don't forward the traffic explicitly, then the firewall will prevent all network traffic from reaching your container.
* `--detach`/`-d`: Run container in background and print container ID
* `--name`: Assign a name to the container

Visit your application on a browser at [localhost:8080][4]. You will see your container is running well. If you want to delete your container,  you can use the following command.

<script src="https://gist.github.com/voltwu/054a475620bf24bb7d96149c5f617384.js"></script>

Or use the `-f`/`--force` to force the removal of a running container, without stopping the container first.

<script src="https://gist.github.com/voltwu/9df3c4262ec00e2210d1d5c1426a7048.js"></script>

# Conclusion
You've studied how to build and run your image, take a review look:
* When you write your Dockerfile, always start with the `FROM` command, follow it with the steps to build up your private filesystem, and conclude with any metadata specifications. You can learn more directives on the [Dockerfile reference][2].

* Each Instructions in the Dockerfile will create a layer in the image. If you changed Dockerfile, and rebuild the image. Docker only rebuilds those layers that changed.

* The `--public` command will forward traffic incoming from the host's port to the container's port.

[1]: https://hub.docker.com/_/nginx
[2]: https://docs.docker.com/engine/reference/builder/
[3]: https://docs.microsoft.com/en-us/powershell/scripting/how-to-use-docs?view=powershell-7
[4]: http://localhost:8080
[5]: https://en.wikipedia.org/wiki/UnionFS
