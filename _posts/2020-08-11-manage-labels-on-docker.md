---
layout: post
title: "Manage labels on docker(container and image)"
date: 2020-08-11 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Manage labels on docker(container and image)'
navi-order: 'a1-3'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: Docker add metadata. Docker object labels mechanism. An Image can have more than one Labels. add your labels with --label flag, filter your label with --filter tag. How to use Docker Labels with Docker images, containers.
excerpt: Docker add metadata. Docker object labels mechanism. An Image can have more than one Labels. add your labels with --label flag, filter your label with --filter tag. How to use Docker Labels with Docker images, containers.
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
In this episode, I'll show you how to add labels on Images and Containers, and filtering them with label tags. When you finished the preceding articles, you may encounter problems that it's too hard to manage enormous containers and images.  Hence, you probably desire to classes those objects(such as containers, images, volumes, and networks) into different types, just for you can found them quickly when you need them.

Here is the label tag. You can put different labels on different objects. For example, you have two containers, one for the testing and one for production purposes. In this scenario, you can label them to different tags, for example, one marked as "test" and the other marked as "production".

# Add labels
You can add labels on either creating or on existing objects.

First, let's look at how to add labels on images. Add the following snippet to your Dockerfile.

<script src="https://gist.github.com/voltwu/ee3a9b80b8239323ed4602c4943ae8a1.js"></script>

After you edit and save successfully, then rebuild your docker image. 

<script src="https://gist.github.com/voltwu/ebaf3d689b4171d73f19cf9444b01338.js"></script>

If you want to view your added labels, use the following command.

<script src="https://gist.github.com/voltwu/83f43863a22a6d9dcfd06ba07764f443.js"></script>

Labels also can legitimately be applied to Containers. You can use the following code to attach labels to your containers.

<script src="https://gist.github.com/voltwu/bb03c15e0ebd5aeea0d2659f4f43c10f.js"></script>

The directive `-l` and `-label` are the same meanings. To add multiple labels, repeat the label flag (`-l` or `--label`). Such as:

<script src="https://gist.github.com/voltwu/7b6ef83f82d5881433f2c39a976ea83f.js"></script>

View your added labels on containers, use the following command.

<script src="https://gist.github.com/voltwu/79ffef93b5c167ef9135147e3d605908.js"></script>

# Filter by labels

When you have applied labels on images and containers, you maybe want filtering with those labels. 
The `--filtering` flag can help you archive this purpose. 
For example, the following snippet only shows dangling images:

<script src="https://gist.github.com/voltwu/c2b4e25bfabb087a589e954e7d4b4e70.js"></script>

It also permitted to filter custom labels, with format "label=value".

<script src="https://gist.github.com/voltwu/2238cc6a7ce876047c6c8d9e1e0ba377.js"></script>

You can do the samething on containers also, check the following snippet:

<script src="https://gist.github.com/voltwu/7d29fb9dfdf651bd09d8a2957523f0ea.js"></script>

If you add a directive `-a` on the end, then it filter in all containers, including stopped containers.


# Update labels
It's easy to update labels on Images. You only need to change the Dockerfile contents, edit the `LABEL` value, and then rebuild your image. 

However, it's a little doozy to update labels on containers. It is because of that changing the settings or code running inside a container involves deleting and restarting the container. And the labels are inside the containers, So, if you want to update your container labels, need to delete or stop the running containers that you want to update labels, and then start a new one with new labels.


