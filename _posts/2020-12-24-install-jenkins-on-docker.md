---
layout: post
title: "Install Jenkins Using Docker"
date: 2020-12-23 09:03:01+0800
categories:
  - Jenkins
tags:
navi-enable-jenkins: true
navi-name: 'Install Jenkins Using Docker'
navi-order: a1-1-1
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: Generally, Jenkins provides two major releases, weekly and lts. Weekly, as the name says, new releases are produced weekly to deliver bug fixes and features to users and plugin developers. lts, every 12 weeks from the stream of regular releases as the stable release for that time period.
excerpt: Generally, Jenkins provides two major releases, weekly and lts. weekly, as the name says, new releases are produced weekly to deliver bug fixes and features to users and plugin developers. lts, every 12 weeks from the stream of regular releases as the stable release for that time period.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-jenkins %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

To get the start, you need to install Jenkins. There are plenty of ways you can achieve this goal. You can build from source code, run war file in Java, or run from a Docker image. etc.

Generally, Jenkins provides two major releases, **weekly** and **lts**. ***Weekly***, as the name says, new releases are produced weekly to deliver bug fixes and features to users and plugin developers. ***lts***, every 12 weeks from the stream of regular releases as the stable release for that time period.

Here, I am gonna illustrate that install Jenkins using Docker. If you run Jenkins using Docker, you have to fit these prerequisites:
1. A256 MB of RAM
2. 1 GB of drive space (although 10 GB is a recommended minimum if running Jenkins as a Docker container)

The next step, create a `docker-compose.yaml` file. And place your instructions into it.

*docker-compose.yaml*
<pre>
version: '3.9'
services:
  jenkins:
    image: jenkins/jenkins:<bold>lts</bold>
    ports:
      - 8080:8080
      - 50000:50000
    volumes:
      - ./jenkins_home:/var/jenkins_home/
    restart: always
    <bold>privileged: true</bold>
    <bold>user: root</bold>
</pre>

The above docker-compose file grants its admin permission by `privileged: true` and `user: root`. And mounting `Jenkins` folder to `/var/jenkins_home` inside the container's instance.  Take a loot at [Official Jenkins Docker image][1] for more details.

Navigate to the same folder where you place the *docker-compose.yaml* file. Then expose your application up.
```
$ docker-compose up -d
```

You can also use docker command directly.
<pre>
docker run \
-v $(pwd)/jenkins_home:/var/jenkins_home  \
-d  \
-p 8080:8080  \
-p 50000:50000  \
-u root \
jenkins/jenkins:lts 
</pre>


<blockquote class="quote">
<b>NOTE:</b>
The above command probably will run your Jenkins web application on a private network. If you can't access your Jenkins remotely(such as: running on a VPS), you can add <code>--net host</code>  to use the <code>host</code> network instead of using the default <code>bridge</code>. You can run <code>docker network list</code> to list all of available network objects on your machine.
</blockquote>

Docker compose with `host` network mode:
<pre>
version: '3.9'
services:
  jenkins:
    image: jenkins/jenkins:lts
    volumes:
      - ./jenkins_home:/var/jenkins_home/
    restart: always
    privileged: true
    user: root
    <red-bold>network_mode: "host"</red-bold>
</pre>
Or using docker command directly.
<pre>
docker run  \
-v $(pwd)/jenkins_home:/var/jenkins_home  \
-d  \
-u root  \
<red-bold>--net host</red-bold>   \
jenkins/jenkins:lts
</pre>
The <red-bold>host</red-bold> network mode instructs docker daemon to use your host's network. So you <b>don't</b> need to specify the <b>ports mapping</b> anymore(beacuase it directly runs on your host's network). Once your Jenkins is running up,  access your Jenkins on the browser at [server_ip:8080][2], the default port is `8080`.

You will see someting like this for setup wizard.
<div class="imgcenter" markdown="1">
![Alt][3]*setup wizard*
</div>
check your initial admin password
<pre>
$ docker exec &lt;containerid&gt; cat /var/jenkins_home/secrets/initialAdminPassword
</pre>
<div class="imgcenter" markdown="1">
![Alt][4]*install plugins*
</div>

<div class="imgcenter" markdown="1">
![Alt][5]*Add first admin user*
</div>

<div class="imgcenter" markdown="1">
![Alt][6]*Jenkins Dashboard*
</div>

Now, a Jenkins instance is fully set up. And in the next articles, I will discuss how to use the Jenkins dashboard.

[1]: https://github.com/jenkinsci/docker
[2]: http://server_ip:8080
[3]: /public/img/2020-12-24-install-jenkins-on-docker-a.png
[4]: /public/img/2020-12-24-install-jenkins-on-docker-b.png
[5]: /public/img/2020-12-24-install-jenkins-on-docker-c.png
[6]: /public/img/2020-12-24-install-jenkins-on-docker-e.png
