---
layout: post
title: "Jenkins Tutorial"
date: 2020-12-23 09:03:01+0800
categories:
  - Jenkins
tags:
navi-enable-jenkins: true
navi-name: 'Jenkins'
navi-order: a1
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: false
description: Jenkins is used for automated continuous integration and continuous deployment. And it is an open-source continuous integration in support of DevOps. In Jenkins, you can set up an automated CI/CD process in a stable, convenient, and fast way.
excerpt: Jenkins is used for automated continuous integration and continuous deployment. And it is an open-source continuous integration in support of DevOps. In Jenkins, you can set up an automated CI/CD process in a stable, convenient, and fast way.
---
<!--navigation bar-->
<div class='navi-link-container'>
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

Jenkins is used for automated continuous integration and continuous deployment. And it is an open-source continuous integration in support of DevOps. In Jenkins, you can set up an automated CI/CD process in a stable, convenient, and fast way.

Let us assume a scenario that many developers commit and push their codes into the project repository. Then a test team continuously tests the codes. If codes pass the test, it will be integrated into the project, otherwise it will be sent back to the developer again for bugs fixing. The development team may push codes several times a day, and the test team may need to test the codes over and over again. This will be tedious for testers, as they do the same test repeatedly. As well as, the deployers also do the same tedious process (such as: same build and deploy operation). 

With Jenkins, all of these processes **test**, **build**, and **deploy** can be configured for **automated actions**. So once developers push their codes into the repository, the Jenkin will automatically pull the latest codes from the same repository, build these codes, test them, and deploy them(if the test **passed**) for your web application.

Let us dive into Jenkins's journey!

<!--items-->
<div>
<span style="color: green;">In this article:</span>
<ul>
  {% assign posts =site.posts | sort: 'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-jenkins %}
      {% assign splitorder = page.navi-order | append: "-" %}
      {% assign splitnum = post.navi-order | split: splitorder %}
      {% assign stagesnumber = splitnum | split: "-" | size %}
      {% assign beginstagesnumber = splitnum | split: "a" | size %}
      {% if stagesnumber == 1 and beginstagesnumber == 1 %}
                <li><a href="{{ site.baseurl }}{{ post.url }}" class="item-link">{{post.title}}</a></li>
      {% endif %}
    {% endif %}
  {% endfor %}
</ul>
</div>
<!--items-->
