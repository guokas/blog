---
layout: post
title: "Docker Hub Webhooks"
date: 2020-12-20 9:05:01+0800
categories:
  - Docker
tags:
  - DevOps
navi-enable-docker: true
navi-name: 'Docker Hub Webhooks'
navi-order: 'a1-4-1-4'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: In the preceding articles that we have talked about Docker Hub Automated Deployment and Docker Hub Automated Tests. In this episode that we continue to talk about the Docker Hub Webhook. Docker Hub allows your webhook triggers to be called when new images were build up. This feature is for continuous deployment. You should configure an open automatic deployment API, which will be used as Docker Hub Webhook.
excerpt: In the preceding articles that we have talked about Docker Hub Automated Deployment and Docker Hub Automated Tests. In this episode that we continue to talk about the Docker Hub Webhook. Docker Hub allows your webhook triggers to be called when new images were build up. This feature is for continuous deployment. You should configure an open automatic deployment API, which will be used as Docker Hub Webhook.
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
In the preceding articles that we have talked about [Docker Hub Automated Build][7] and [Docker Hub Automated Tests][8]. In this episode that we continue to talk about the Docker Hub Webhook. Docker Hub allows your webhooks to be triggered when new images were build up. This feature is for continuous deployment(CD). You should set up an open automated deployment web API, which will be used as Docker Hub Webhook.

# Create An Deployment API
This API depends on your deployment workflow, such as whether if you are using k8s or [running a single docker image][10]. Whatever types of API you are going to build, you should expose that API to the Internet. So the DockerHub can access your API.

I found some useful tools that can help developers deploy automatic deployment process API. 
1. [webhook.site][2]: On their website, you can get a link. And this website will list out all of the payloads of your link. So it can help you analyze your payload.
2. [Docker Webhook][3]: This is an open-source application fork from [Praisebetoscience/docker hub-webhook][4] that is a simple server-side python script in a Flask framework. 

I built an automated deployment process using [Docker Webhook][3] and my [docker hub cd script][5] `docker-hub-cd-script.sh`.
```
#! /bin/sh
script_name=docker-hub-cd-script.sh

for pid in $(pidof -x $script_name); do
    if [ $pid != $$ ]; then
        kill -9 $pid
    fi 
done

docker container stop docker-hub-automated-build-example

docker container rm docker-hub-automated-build-example

docker image rm -f davidwxg/docker-hub-automated-build-example:latest

docker run -p 8000:8000 -d --name docker-hub-automated-build-example davidwxg/docker-hub-automated-build-example:latest
```
This shell script will kill other instances of this script. Stop and remove the running container `docker-hub-automated-build-example`, Clean local images `davidwxg/docker-hub-automated-build-example:latest`, and running up a container with the latest remote image.

Then configure this script in `config.py` in [Docker Webhook][3] application
```
HOOKS = {
         'repo1': {'latest':'docker-hub-cd-script.sh'}
        }
```

You can get more information on [Open source Docker Webhook][3] and [docker-hub-CI-CD-explanation][6].

# Set up Webhooks
Open your docker hub dashboard, navigate to *webhooks*.
<div class="imgcenter" markdown="1">
![Alt][1]*building logs*
</div>

This article discussed the Docker Hub webhook and according to the previous two articles [Docker Hub Automated Builds][7] and [Docker Hub Automated Tests][8]. We built up a comprehensive, automated deployment system. Hope you can enjoy this, if you have any questions or advisements, please be free to comments at [DISQUS][9] in the below.

[1]: /public/img/2020-12-20-docker-hub-webhooks-a.png
[2]: https://webhook.site/
[3]: https://github.com/voltwu/docker-hub-webhook
[4]: https://github.com/Praisebetoscience/dockerhub-webhook
[5]: https://github.com/voltwu/docker-hub-CI-CD-explanation/blob/main/webhooks/docker-hub-cd-script.sh
[6]: https://github.com/voltwu/docker-hub-CI-CD-explanation
[7]: https://voltwu.github.io/blog/docker/2020/12/15/docker-hub-automated-build/
[8]: https://voltwu.github.io/blog/docker/2020/12/16/docker-hub-automated-test/
[9]: https://disqus.com/
[10]: https://voltwu.github.io/blog/docker/2020/07/08/build-and-run-your-image/