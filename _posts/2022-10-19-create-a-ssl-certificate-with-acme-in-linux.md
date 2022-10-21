---
layout: post
title: "Create SSL certificates with acme in Linux"
date: 2022-10-19 10:01:01+0800
categories:
tags:
  - SSL Certificates
navi-name: 'Create SSL certificates with acme in Linux'
navi-order:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: 
excerpt: 
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-mssql %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->
Deploying an HTTPS website requires you to have SSL certificates. The ramification of not being protected by SSL certificates is significant, and worldwide developers are using SSL certificates to encrypt the data transition of their websites. Reads official documents from scratch that can make people depressed. Today, I will illustrate a simple and effective way to create SSL certificates with acme.sh.

# acme.sh installation
I am running the installation based on Debian Linux, and I already had Nginx installed on my Linux machine. 
```shell
curl https://get.acme.sh | sh
```

# Issue your domain
```shell
~/.acme.sh/acme.sh  --issue  -d example.com  --webroot /usr/share/nginx/html/
```

# Install certificate
```shell 
~/.acme.sh/acme.sh --installcert -d example.com --key-file /etc/nginx/ssl/private.key --fullchain-file /etc/nginx/ssl/cert.crt
```

# Review SSL certs
```shell 
~/.acme.sh/acme.sh --renew -d example.com --force
```

# Update acme.sh
```shell 
~/.acme.sh/acme.sh --upgrade --auto-upgrade
```

if you want assign read, write, and execute permission to the user, run the command `chmod -R 755 /etc/nginx/ssl/`

fullscript:
```bash 
curl https://get.acme.sh | sh

~/.acme.sh/acme.sh  --register-account -m example@gmail.com

~/.acme.sh/acme.sh  --issue  -d example.com  --webroot /usr/share/nginx/html/

~/.acme.sh/acme.sh --installcert -d example.com --key-file /etc/nginx/ssl/private.key --fullchain-file /etc/nginx/ssl/cert.crt

~/.acme.sh/acme.sh --upgrade --auto-upgrade

chmod -R 755 /etc/nginx/ssl/
```