---
layout: post
title: "Add User and Password Authentification"
date: 2020-09-30 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Add User and Password Authentification'
navi-order: 'a1-4-2-3'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: In the previous article, we do achieve access registry from other hosts, but it's insecure. The data transform between server and client can be sniffed or modified by malicious hackers. So, In this article, I will show you the first step for securing is that use an SLL certificate for production.
excerpt: In the previous article, we do achieve access registry from other hosts, but it's insecure. The data transform between server and client can be sniffed or modified by malicious hackers. So, In this article, I will show you the first step for securing is that use an SLL certificate for production.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1" 
        or post.navi-order == "a1-4"
        or post.navi-order == "a1-4-2" %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

At present, through the previous two articles, you have a public and SSL secured registry for against network sniff attack. However, this is not enough for private registries. Any private registries should have some kind of authentification and restrict access, and only authorized users can access the registry.  The authentification and restrict access mitigates the risk of abuse.  

This article leads you to achieve basic authentification most simply through htpasswd. You can distribute users and their passwords, and users must do authentification first before do any registry operation.

# Prerequisites
1. An open and SSL protected registry is running up. If you don't know how to set up, please review the previous two articles.

# Let's start
It's easy to set up a user and password authentification through the htpasswd module, which is the Apache's constituent. 
There are two steps for setting up authentification, first is to generate your password file, and then apply your password file to your registry container.

1. Generate a password file
```
$ mkdir auth
$ docker run \
  --entrypoint htpasswd \
  registry:2.7.0 -Bbn youruser yourpassword > auth/htpasswd
```
The above snippet created a user `youruser` and password `yourpassword` in the file `auth/htpasswd` in the `registry:2.7` image. 
<blockquote class="quote">
<p>
<b>NOTE:</b> 
<p>
All docker args come before the docker image. Whose args are for <code>htpasswd</code>, which is the <code>-Bbn</code>. 
</p>
<p>
The <code>-Bbn</code> represents to use bcrypt, run in bash, and standard output. See the <a href="https://httpd.apache.org/docs/2.4/programs/htpasswd.html">htpasswd - Manage user files for basic authentication</a> for more information.
</p>
<p>
Don't use <code>registry:2.7</code> for the password file generate, because <code>htpasswd</code> doesn't exist in the <code>registry:2.7</code> image. You should use <code>registry:2.0</code>, <code>registry:2.7.0</code>, or others instead.
</p>
</p>
</blockquote>
Once you finished. It'll create a htpasswd file in your local machine, view [here][2] to explore more details about the working mechanism of the above snippet.
```
$ ls auth
htpasswd
$ cat auth/htpasswd
youruser:$2y$05$EmtDysYM8i42jUWp6qXg1.nSENd/b.A2ytile0TVETWzTfP4N/mp6
```

2. Apply your password file to the registry container
You can use the following snippet to apply your password file on your `registry:2.7.0` container.
```
$ docker run -d \
  --restart=always \
  --name registry \
  -v "$(pwd)"/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v /etc/letsencrypt:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/live/registry.vigourwu.xyz/fullchain.pem \
  -e REGISTRY_HTTP_TLS_KEY=/certs/live/registry.vigourwu.xyz/privkey.pem \
  -p 5050:5000 \
  registry:2.7.0
```
Now, finally. A password secured registry is running up. 
```
$ docker login registry.vigourwu.xyz:5050
$ docker tag ubuntu registry.vigourwu.xyz:5050/ubuntu
$ docker push registry.vigourwu.xyz:5050/ubuntu
$ docker pull registry.vigourwu.xyz:5050/ubuntu
$ docker logout registry.vigourwu.xyz:5050/ubuntu
```

# Conclusion
At this point, you know how to set up a password secured registry.  I have mentioned all the details in the process. By the way, in the previous article [Set up a docker registry on debian 4.6][3], we built up a public registry without SSL and password, it should only be used in the testing environment. However, now our registry secured over SSL and password fits for the production environment.

If you have any questions, please feel free to comments.

[1]: https://httpd.apache.org/docs/2.4/programs/htpasswd.html
[2]: https://stackoverflow.com/questions/64112838/what-does-bbn-means-in-docker/64112969#comment113561122_64112969
[3]: https://voltwu.github.io/blog/docker/2020/09/10/set-up-a-private-docker-registry-on-debian/