---
layout: post
title: "Docker Tutorial"
date: 2020-07-08 09:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Docker'
navi-order: a1
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: Docker is a container technique, which is lightweight and has a lower overhead than VM. By contrast, VM(eg.VMware and KVM) is a full virtual operating system that runs depends on virtual hardware, VM needs to emulate hardware and transfer users' operation between the host and visual machine. So VM commonly has a higher overhead than containers.
excerpt: Docker is a container technique, which is lightweight and has a lower overhead than VM. By contrast, VM(eg.VMware and KVM) is a full virtual operating system that runs depends on virtual hardware, VM needs to emulate hardware and transfer users' operation between the host and visual machine. So VM commonly has a higher overhead than containers.
---
<!--navigation bar-->
<div class='navi-link-container'>
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->
Let's take a brief look at the docker container. 

Docker is similar to Virtual Machines. Both of them can emulate operating system environments. But, instead of a full operating system on Virtual Machines, the docker is only a set of processes that running on a shared OS kernel.

Docker is a container technique, which is lightweight and has a lower overhead than VM. By contrast, VM(eg.[VMware][2] and [KVM][1]) is a full virtual operating system that runs depends on virtual hardware, VM needs to emulate hardware and transfer users' operation between the host and virtual machine. So VM commonly has a higher overhead than containers.

[1]: https://www.linux-kvm.org/page/Main_Page
[2]: https://www.vmware.com/

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
      {% if post.navi-order == "a1-1" or
            post.navi-order == "a1-2" or 
            post.navi-order == "a1-3" or 
            post.navi-order == "a1-4" or 
            post.navi-order == "a1-5" or 
            post.navi-order == "a1-6" or 
            post.navi-order == "a1-7" or
            post.navi-order == "a1-8" %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->
