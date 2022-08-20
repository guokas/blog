---
layout: post
title: "How to Install Jenkins On Debian 9 Linux"
date: 2020-12-29 09:03:01+0800
categories:
  - Jenkins
tags:
navi-enable-jenkins: true
navi-name: 'How to Install Jenkins On Debian 9 Linux'
navi-order: a1-1-2
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: Jenkins is an automation server, which can be installed through Docker or other packages. In the previous blog, I introduced how to install Jenkins on Docker Compose. In this article, I will focus on how to install Jenkins on Debian 9 Linux System. If you don't Jenkins, you can take a look at my Jenkins Tutorial for a short description.
excerpt: Jenkins is an automation server, which can be installed through Docker or other packages. In the previous blog, I introduced how to install Jenkins on Docker Compose. In this article, I will focus on how to install Jenkins on Debian 9 Linux System. If you don't Jenkins, you can take a look at my Jenkins Tutorial for a short description.
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

Jenkins is an automation server, which can be installed through Docker or other packages. In the previous blog, I introduced how to install Jenkins on Docker Compose. In this article, I will focus on how to install Jenkins on Debian 9 Linux System. If you don't Jenkins, you can take a look at my Jenkins Tutorial for a short description.

# Install Java
Jenkins depends on Java application, which you should be installed as a prerequisite. There are many ways that you can achieve a Java environment implementation. The most common way is OpenJDK, which will be used to install for this article.

Open Java Development Kit(OpenJDK) Installation.

* search of all available jdk packages:
```
$ apt update
$ apt search openjdks
```

* Pick openjdk 8 or higher version:
```
$ apt install openjdk-8-jdk
```

# Install Jenkins
Jenkins provides two main releases, they are `lts` and `weekly`. Both of them are available to be installed, the `weekly` release produces weekly for bug fixes and features to users and plugin developers. However, the `lts` choose from every twelve weeks from regular releases as a stable release for that time period. 

**Install LTS Version**
```
$ wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
$ sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
$ sudo apt-get update
$ sudo apt-get install jenkins
```

**Install Weekly Version**
```
$ wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
$ sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
$ sudo apt-get update
$ sudo apt-get install jenkins
```

# Manage Jenkins Service

**start Jenkins**
```
$ systemctl start jenkins
```

**Jenkins status**
```
$ systemctl status jenkins
```

**Stop Jenkins**
```
$ systemctl stop jenkins
```

**Disable Jenkins on startup**
```
$ systemctl disable jenkins
```

**Enable Jenkins on startup**
```
$ systemctl enable jenkins
```

**Uninstall Jenkins**
```
$ apt-get remove jenkins
$ rm -rf /var/lib/jenkins
```

# Where is the Jenkins Home Directory
By default, the Jenkins home directory located at `/var/lib/jenkins` on Linux. 
```
$ ls /var/bin/jenkins
```
When your Jenkins server running up, you can use another location instead of the default one on the Jenkins dashboard.

# Set up Wizard
Opens [server_ip:8080][2] at your browser, the default port is `8080`, and you can change it in `/etc/default/jenkins`.

<div class="imgcenter" markdown="1">
![Alt][3]*initial password*
</div>
Check initual admin password.
```
$ cat /var/lib/jenkins/secrets/initialAdminPassword
```
Copy the passord and paste it at *administrator password*, then click *Continue*.

<div class="imgcenter" markdown="1">
![Alt][4]*Install Plugins*
</div>

<div class="imgcenter" markdown="1">
![Alt][5]*Add first admin user*
</div>

<div class="imgcenter" markdown="1">
![Alt][6]*Jenkins Dashboard*
</div>

[2]: http://server_ip:8080
[3]: /public/img/2020-12-29-how-to-install-jenkins-on-debian-9-a.png
[4]: /public/img/2020-12-29-how-to-install-jenkins-on-debian-9-b.png
[5]: /public/img/2020-12-29-how-to-install-jenkins-on-debian-9-c.png
[6]: /public/img/2020-12-29-how-to-install-jenkins-on-debian-9-d.png
