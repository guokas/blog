---
layout: post
title: "Using Nexus3 as your repository - A Simple Guide(No Deep Shit!)"
date: 2021-01-21 10:03:01+0800
categories:
  - Docker
tags:
  - Repository Manager
navi-enable-docker: true
navi-name: 'Using Nexus3 as your repository - A Simple Guide(No Deep Shit!)'
navi-order: 'a1-4-3-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: set up a simple nexus3 instance. 
excerpt: set up a simple nexus3 instance. 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

In this article, I will guide you to set up a simple nexus3 instance. 

# Running nexus3 on docker
I am using the **sonatype/nexus3** image for demonstration. There are multiple ways that you can set up a nexus3 instance. You can either unpack the archive in a directory to which you have full access, or you can install it with a Docker image. Take a look at [sonatype Nexus Repository Manager 3 Installation][1] for more details.

The following **docker-compose.xml** utilize the <bold>sonatype/nexus3</bold> image to achieve the installation.
<pre>
version: "3.3"
services:
  nexus3:
    restart: always
    image: <bold>sonatype/nexus3</bold>
    ports:
      - "8081:8081"
    volumes:
      - nexus-data:/nexus-data
volumes:
    nexus-data:
        driver: local
        driver_opts:
            type: 'none'
            o: 'bind'
            device: '${PWD}/nexus-data'
</pre>

You should create a folder `nexus-data` in your current directory at first, then use command `docker-compose up -d` to running up. The `nexus-data` folder will be used to store your persistent data.

```
$ mkdir nexus-data
$ docker-compose up -d
```

# Creating A Repository

create a docker repository

make your docker repository private



# Pushing and Pulling Images


[1]: https://help.sonatype.com/repomanager3/installation