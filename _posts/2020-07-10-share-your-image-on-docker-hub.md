---
layout: post
title: "Share your Images on Docker Hub"
date: 2020-07-08 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Share your Images on Docker Hub'
navi-order: 'a1-2-3'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: You have created a containerized application. This article will show you how to share your images on the Docker Hub step by step. Note all images on Docker Hub are public except private. Go the Docker Hub Sign Up page, then type your Docker ID and finish other requirements. Click the create repository button, enter a name.
excerpt: You have created a containerized application. This article will show you how to share your images on the Docker Hub step by step. Note all images on Docker Hub are public except private. Go the Docker Hub Sign Up page, then type your Docker ID and finish other requirements. Click the create repository button, enter a name.
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

You have created a containerized application. This article will show you how to share your images on the Docker Hub step by step. Note all images on Docker Hub are public except private. 

# Sign up Docker Hub Page

Go the [Docker Hub Sign Up page][1], then type your `<Docker ID>` and finish other requirements.

# Create a repository

Click the create repository button, enter a name.

<blockquote class="quote">
<p>
<b>NOTE:</b> You can choose your repository whether is a public repository. A public repository can be used by anyone. If you want to secret your repository, then you can make your repository private. 
</p>
</blockquote>

# Tag your image

Tag your image, before you push your image into the remote repository. Your tag name should be like this `<Your Docker ID>/<Repository Name>:<tag>`.

<script src="https://gist.github.com/voltwu/17499f229bd70d729c53aa1377904050.js"></script>

`docker tag` is basically to create an alias from another image. The above code will create a new alias `<Your Docker ID>/tweet_app:1.0` of the `tweet_app:1.0` image. After this, you will have two names `tweet_app:1.0` and `<Your Docker ID>/tweet_app:1.0`. You can run `docker image ls` to list all your images.
You can omit your `tag` part, if you do that, your `tag` will be the `latest` by default.

<script src="https://gist.github.com/voltwu/c131228083bff4e08615dc1f44409212.js"></script>

The above code will create a new image named as `<Your Docker ID>/tweet_app:latest`, the `tag` value is `latest`. The following code is the same meaning:
<script src="https://gist.github.com/voltwu/5106ef5cd7a26924762bd71e9f54f504.js"></script>

# Push your image to the repository

You have to log in at command line first. Type the `docker login` command to log in, or `docker logout` to log out. After you login successfully, then you can run the `docker push <Your Docker ID>/<repository>:tag` to push your images to the remote repository.

<script src="https://gist.github.com/voltwu/ebfb60a854dc387770b9f4af75216330.js"></script>

if you are in the `latest` tag, you can omit it.
<script src="https://gist.github.com/voltwu/4ddaeb699e057a441c980c6ad627f3ba.js"></script>


# Pull your image back

Now your Image is on Docker Hub available. You can pull your image back in many ways eg. `FROM` in a Dockerfile, or `docker pull` in the command line environment. The following snippet shows you the `docker pull`.

<script src="https://gist.github.com/voltwu/e08eecf9ac43b4fed44033fa6bcfedfc.js"></script>


[1]: https://hub.docker.com/signup
[2]: /public/img/2020-07-10-share-your-image-on-docker-hub-a.png
