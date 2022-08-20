---
layout: post
title: "Secure your registry with SSL certificate"
date: 2020-09-21 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Secure your registry with SSL certificate'
navi-order: 'a1-4-2-2'
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

In the previous article, we do achieve access registry from other hosts, but it's insecure. The data transform between server and client can be sniffed or modified by malicious hackers. So, In this article, I will show you the first step for securing is that use an SLL certificate for production.

# Prerequisite
* A running Docker registry.
* A domain name or subdomain name.

# Get Certificate
There are a couple of ways that you can get a certificate. You can buy from the certification authority(CA) or obtain it from free websites([Let's Encrypt][1], [SSL For Free][2]...).  

Here, I generated a certificate for my domain from *Let's Encrypt* command shell. *Let's Encrypt* command shell is convenient for generating public certificates. With a few steps, you'll get your certificate. Once you finish, you'll have the following files under `/etc/letsencrypt/live/registry.vigourwu.xyz`.
```
$ pwd
/etc/letsencrypt/live/registry.vigourwu.xyz
$ ls
cert.pem  chain.pem  fullchain.pem  privkey.pem  README
```
You'll get five files as the above, open the `README` file. And it'll tell you each file's details. Here, the `fullchain.pem` is a certificate file, and `privkey.pem` is a private key file,  and when you use them, you don't need to change the file name, just use it directly.

<blockquote class="quote">
<p>
<b>NOTE:</b> Generate an SSL certificate from an IP address is possible, but it's rarely used. Certificates attached to IP address faces revoking risk, and it also doesn't have any help for DNS cache. For more see <a href="https://stackoverflow.com/questions/2043617/is-it-possible-to-have-ssl-certificate-for-ip-address-not-domain-name">Is it possible to have ssl certificate for ip address not domain?</a>.
</p>
</blockquote>

# Run your registry up with SSL certificate
There are plenty of ways you can set your SSL certificate over your registry connection. You can use Ngnix, Composefile ect.

In the following example, I'll use the `docker run` command with `REGISTRY_HTTP_TLS_CERTIFICATE` and `REGISTRY_HTTP_TLS_KEY` variable to set up an SSL connection.
```
docker run -d \
  --restart=always \
  --name registry \
  -v /etc/letsencrypt:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:port_container \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/live/your.domain/fullchain.pem \
  -e REGISTRY_HTTP_TLS_KEY=/certs/live/your.domain/privkey.pem \
  -p port_host:port_container \
  registry:2.7
```
Replace the `your.domain` with your domain, and the `port_host` with your host's port, and `port_container` with your container's port. You can specify your mount path through `-v` variable. In the above mount example, you should make sure your certificate is under the path `/etc/letsencrypt/live/your.domain` in your hostmachine.

The following is my whole code snippet.
```
docker run -d \
  --restart=always \
  --name registry \
  -v /etc/letsencrypt:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/live/registry.vigourwu.xyz/fullchain.pem \
  -e REGISTRY_HTTP_TLS_KEY=/certs/live/registry.vigourwu.xyz/privkey.pem \
  -p 5050:5000 \
  registry:2.7
```

Once your registry is running up, you can access your registry from other hosts with a secure connection over SSL. In the [previous article][6], we've discussed how to add insecure connection to a docker daemon. Here, As you are using SSL protected, so it is a secure connection, and hence you don't need to add the registry address on the client docker daemon in the insecure connection configuration.

As you can see from the above, I mounted the path `/etc/letsencrypt` in local machine to `/certs` in the container. Don't mount the path that includes dots, which may cause unexpected errors.

The following command got failed on my machine.
```
$ docker run \
  --restart=always \
  --name 2registry \
  -v /etc/letsencrypt/live/registry.vigourwu.xyz:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/fullchain.pem \
  -e REGISTRY_HTTP_TLS_KEY=/certs/privkey.pem \
  -p 5051:5000 \
  registry:2.7
time="2020-09-28T05:49:36.509990307Z" level=warning msg="No HTTP secret provided - generated random secret. This may cause problems with uploads if multiple registries are behind a load-balancer. To provide a shared secret, fill in http.secret in the configuration file or set the REGISTRY_HTTP_SECRET environment variable." go.version=go1.11.2 instance.id=5a8e4808-6c2c-4104-ac0b-34ae23778442 service=registry version=v2.7.1 
time="2020-09-28T05:49:36.511286456Z" level=info msg="redis not configured" go.version=go1.11.2 instance.id=5a8e4808-6c2c-4104-ac0b-34ae23778442 service=registry version=v2.7.1 
time="2020-09-28T05:49:36.510525597Z" level=info msg="Starting upload purge in 12m0s" go.version=go1.11.2 instance.id=5a8e4808-6c2c-4104-ac0b-34ae23778442 service=registry version=v2.7.1 
time="2020-09-28T05:49:36.526651206Z" level=info msg="using inmemory blob descriptor cache" go.version=go1.11.2 instance.id=5a8e4808-6c2c-4104-ac0b-34ae23778442 service=registry version=v2.7.1 
time="2020-09-28T05:49:36.527667014Z" level=fatal msg="open /certs/fullchain.pem: no such file or directory"
```
This is because that the docker isn't very compatible with dots. Hope docker will fix this bug in the future. You can check whether your docker support dots. But I highly recommended that don't use dots or any other special character in your mounted path.

SSL Checker is a great tool for checking is if a website has an SSL certificate on. If you follow the above steps strictly, then you'll see all green right symbols on the [SSL Checker][8](There should not be any break symbols). Just as the following image.
<div class="imgcenter" markdown="1">
![Alt][7]*registry.vigourwu.xyz:5050 protected with SSL*
</div>

Once your Docker registry is running up, you can access your registry with secure connection from other hosts as the following snippet:
```
$ docker pull redis
$ docker tag redis registry.vigourwu.xyz:5050/redis
$ docker push registry.vigourwu.xyz:5050/redis
```

[1]: https://letsencrypt.org/
[2]: https://www.sslforfree.com/
[4]: https://stackoverflow.com/questions/2043617/is-it-possible-to-have-ssl-certificate-for-ip-address-not-domain-name
[5]: https://your.domain:port
[6]: /docker/2020/09/10/set-up-a-private-docker-registry-on-debian/
[7]: /public/img/2020-09-21-secure-your-registry-with-ssl-a.png
[8]: https://www.sslshopper.com/ssl-checker.html