---
layout: post
title: "Docker Hub Introduction"
date: 2020-12-14 8:01:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Docker Hub Introduction'
navi-order: 'a1-4-1-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: what's is the docker hub repository. How to use Docker hub. Docker hub beginner tutorial. Everything you need to know about docker hub. Docker Hub is a service provided by Docker, it provides repositories, team&organization, offical images, publisher images, builds and webhook.
excerpt: what's is the docker hub repository. How to use Docker hub. Docker hub beginner tutorial. Everything you need to know about docker hub. Docker Hub is a service provided by Docker, it provides repositories, team&organization, offical images, publisher images, builds and webhook.
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

In this blog, I'm gotta show you how to use the docker hub. As the [Share your images on docker hub][1] has already explained its principal functionalities(take a look at [Share your images on docker hub][1] if you are not familair), and hence, I won't explain it here again. This blog will explain other cool things about the docker hub for you. If you have any doubts or questions, please feel free to comments.

The primary thing is to sign up for the [docker page][2]. Finish all requirements and email verification, then when you have done, sign in your account and access your dashboard.

<div class="imgcenter" markdown="1">
![Alt][3]*Docker dashboard*
</div>

As you can see from the above dashboard, the principal advantages of docker page include that provide plenty of images, store and retrieve images, secure your account, etc. 

When you create your repository, you can decide whether it's private or public. A private repository means that only you can view it. Instead, a public repository means that anyone can view it. At present, the Docker hub is not completely free, and it provides two plan types, one is the free plan, the other one is the premium plan. The free plan only allows you to create only one private repository, and the premium doesn' t  have the limitation on the numbers of creating private repositories. 

The Explore area provides you all public repositories, such as those public repositories that you created.  Note that the private repository won't list in the search result.

The Docker Hub also allows you to create an organization, which enables you to control who can create or view repositories as well as push or pull image changes.  

If you have any questions, you can take a look at the [documentation][4], or ask for help on [docker forums][5].

[1]: /docker/2020/07/08/share-your-image-on-docker-hub/
[2]: https://hub.docker.com/
[3]: /public/img/2020-08-14-docker-hub-a.png
[4]: https://docs.docker.com/docker-hub/
[5]: https://forums.docker.com/