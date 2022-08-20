---
layout: post
title: "Warm-up: Build a .net core console app"
date: 2020-07-13 09:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Warm-up: Build a .net core console app'
navi-order: 'a1-9-1-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: Docker is a container technique, which is lightweight and convenient, and it has become a massively popular technology. What are you waiting for, let’s explore it together! This article will show you a simple, which you can learn how to build a .net Core Console application in Docker step by step.
excerpt: Docker is a container technique, which is lightweight and convenient, and it has become a massively popular technology. What are you waiting for, let’s explore it together! This article will show you a simple, which you can learn how to build a .net Core Console application in Docker step by step.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1" or
            post.navi-order == "a1-9" or
            post.navi-order == "a1-9-1"
            %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

Docker is a container technique, which is lightweight and convenient, and it has become a massively popular technology. What are you waiting for, let's explore it together!

This article will show you a simple, which you can learn how to build a .net Core Console application in Docker step by step.

# Prerequisites
* Familiar with [Docker][2]
* Familiar with [.NET CORE][5]

# Build and Publish a console App
You can choose different editors, like VSCode, Visual Studio, etc. This article builds an App through the command-line interface. Make sure you have [.NET CORE SDK][1] installed first.

Use the following snippet to create a .NET Core app. 

<script src="https://gist.github.com/voltwu/a496e1bfdbb9846f3ddd33bbc0e43e7e.js"></script>

Get into `MyApp` folder, then run your App.

<script src="https://gist.github.com/voltwu/b8fee310d708a007670c09b5c06267b3.js"></script>

Publish your App.

<script src="https://gist.github.com/voltwu/8483789cb18d6a88316753dd86f8132f.js"></script>

Get into your released folder, then run your `.dll` file up. 

<script src="https://gist.github.com/voltwu/e295ec25e55561cb3e96f2e82406be36.js"></script>

# Containerize
Create a `Dockerfile` with the following snippet to containerize your released App.

<script src="https://gist.github.com/voltwu/2e02f788d87882253defd5082b22c8e3.js"></script>

Put the above Dockerfile under `MyApp` directory.

<script src="https://gist.github.com/voltwu/a83a39a91085fb9f7d029fe07a8a4ba5.js"></script>

Build an image.

<script src="https://gist.github.com/voltwu/4aefedf217a630eb0a00267378497bc2.js"></script>

Run your image as container.

<script src="https://gist.github.com/voltwu/7d22860f02f5abbbd261a4cbd3a3d3f4.js"></script>

Now, your container is running up successfully. If you want to extend more, check the [Docker][2]. 

The above process only containerized your released App. As the Docker is a container, so you can do the same thing as you are in a real operating system. You can build, publish, release your App in a single Docker container. Even though you can do multi-steps in a single docker container, however, it is not recommended. It will be hard to manage and track stages when doing too many steps in a single docker container. You can take advantage of the multi-stage feature, which is much more flexible and convenient.

The following Dockerfile illustrate a multi-stage example in a single Dockerfile.

<script src="https://gist.github.com/voltwu/96f5c2edbfb2f6f28a39450b9e7e046f.js"></script>

The above Dockerfile comes from Visual Studio Docker Tools, and the above Dockerfile related to `multistage` features, which was introduced in docker 17.05. Check these articles [How Visual Studio builds containerized apps][3], and [Use multi-stage builds][4] to learn more about Docker and Visual Studio.

<script src="https://gist.github.com/voltwu/4237a9db898dca0d1aa5756d7d69a40c.js"></script>

Use the `-f` to specify the Dockerfile explicitly.

# Conclusion
Through this tutorial, you've learned how to deploy NET CORE Console in Docker. Take a look review.

1. Docker is a container, which provides all the necessary packages you need. You can configure your App to be built, run, release in a single Dockerfile(use the `multistage` feature).
2. Visual Studio Docker Support Tools takes the advantages of the Docker's `multistage` features.
3. If you want to learn more about Docker, see [Docker Tutorial][2].


[1]: https://dotnet.microsoft.com/download
[2]: /docker/2020/07/08/docker-tutorial/
[3]: https://docs.microsoft.com/en-us/visualstudio/containers/container-build?view=vs-2019
[4]: https://docs.docker.com/develop/develop-images/multistage-build/
[5]: https://docs.microsoft.com/en-us/dotnet/core/
