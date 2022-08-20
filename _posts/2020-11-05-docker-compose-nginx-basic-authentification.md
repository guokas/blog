---
layout: post
title: "Deploy with Docker Compose and Nginx basic authentification"
date: 2020-11-05 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Deploy with Docker Compose and Nginx basic authentification'
navi-order: 'a1-4-2-4'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: we had set up a registry server with basic authentification. However, it all depends on the Docker commands, which maybe seems a little tedious. Another disadvantage of only work with docker commands is that the docker isn't that flexible for advanced authentification at present. Hence, I am going to introduce Ngnix for the next coming articles for much-advanced authentification. As I mentioned in the title, this article only illustrates the basic authentification, no advanced in here. I am going to use another way(Compose and Ngnix) to implements an authentification mechanism that is analogous to that of as we did in the previous three articles. So, this article max together all we learned in the preceding registry articles. If you have any problems during this article, don't worry, you can follow the guild hint I marked to explore more details.
excerpt: we had set up a registry server with basic authentification. However, it all depends on the Docker commands, which maybe seems a little tedious. Another disadvantage of only work with docker commands is that the docker isn't that flexible for advanced authentification at present. Hence, I am going to introduce Ngnix for the next coming articles for much-advanced authentification. As I mentioned in the title, this article only illustrates the basic authentification, no advanced in here. I am going to use another way(Compose and Ngnix) to implements an authentification mechanism that is analogous to that of as we did in the previous three articles. So, this article max together all we learned in the preceding registry articles. If you have any problems during this article, don't worry, you can follow the guild hint I marked to explore more details.
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
Through the previous three articles, we had set up a registry server with basic authentification. However, it all depends on the Docker commands, which maybe seems a little tedious. Another disadvantage of only work with docker commands is that the docker isn't that flexible for advanced authentification at present. Hence, I am going to introduce Ngnix for the next coming articles for much-advanced authentification.

As I mentioned in the title, this article only illustrates the basic authentification, no advanced in here. I am going to use another way(Compose and Ngnix) to implements an authentification mechanism that is analogous to that of as we did in the previous three articles. So, this article max together all we learned in the preceding registry articles. If you have any problems during this article, don't worry, you can follow the guild hint I marked to explore more details.

# Prerequisites
1. Docker Installed
2. Docker Composed Installed
3. A domain and SSL certificate files
4. Nginx Installed
5. the htpasswd file

First, you have to have the Docker and Docker-Compose installed on your machine. Docker Compose is just a tool, which makes docker much more convenient to use. Take a look at the [official document][1] for more detailed information.

A domain is also required, which is for generating certificate files for secure connection over https. As I mentioned in [Secure your registry with SSL certificate][2], https over IP connection is also possible, but it faces some revoking risks. Here, I highly recommend having a domain name. You can follow the [guide][2] for generating your domain's certificate files.

Another required component is the Nginx, which we are going to use its basic authentification and traffic forwarding functionalities. So make sure you had Nginx installed on your machine.

The htpasswd file stores the users and their passwords, then use this file in the Nginx or Docker Registry Image for achieving a basic authentication(Nginx authentication in this example). In the previous blog, I've shown how to generate a htpasswd file from a docker image, which will redirect the outputs to the user's local machine. But it seems tedious if your image doesn't have the `htpasswd` unity available. Here, I'll generate a htpasswd file on my local, as I am working on Debian, so I need to install the apache utils packages(the `htpasswd` unity is in the `apache2-utils` package for `Debian`, `Ubuntu`, or `httpd-tools` package for `RHEL`/`CentOS`/`Oracle` Linux).
```
$ cd ~
$ mkdir -p registry/auth && cd registry/auth
$ apt-get update
$ apt-get install -y apache2-utils
$ htpasswd -c htpasswd user
```

Follow the prompt for the password, and it will save the `user` and its password in the `htpasswd` file(with the `-c` flag to create a new file). If you want to create additional user-password pairs, then omit the `-c` flag because the file already exists.

<blockquote class="quote">
<p>
<b>NOTE:</b>
<p>If you are running Nginx out of the docker image. I highly don't recommend to use bcrypt, because Nginx doesn't support very well on bcrypt password. Since bcrypt is not supported within <code>auth_basic</code> ATM, mysterious 500 errors can be found in nginx <code>error.log</code>, (usually found at <code>/var/log/nginx/error.log</code>). For more information, take a look at <a href="https://stackoverflow.com/questions/31833583/nginx-gives-an-internal-server-error-500-after-i-have-configured-basic-auth">Nginx gives an Internal Server Error 500 after I have configured basic auth</a> </p>
</p>
</blockquote>

# Let's start
Start your registry with a Compose file. If you are unfamiliar with Docker Compose, take a look at [docker compose][3].
```
$ cd ~ 
$ madir -p registry/data && cd registry
$ touch docker-compose.yml
```
Open your `docker-compose.yml` file, paste the following snippet into it.
```
version: "3.3"
services:
        registry:
                restart: always
                image: registry:2.7
                ports:
                        - 5000:5000
                volumes:
                        - ./data:/var/lib/registry
```
Then Running your registry up through `docker-compose up`. 

Now, you've got a registry running up. The next step is to set a traffic forwarding to [localhost:5000](localhost:5000) through the Nginx.

**1. Copy certificate and the htpasswd into under the nginx**
```
$ # copy htpasswd
$ cd ~/registry
$ cp -r auth /etc/nginx/conf.d
$ 
$ # copy certificate
$ mkdir -p /etc/nginx/conf.d/cert
$ cp /etc/letsencrypt/live/your.domain /etc/nginx/conf.d/cert
```

Let's take a look at the file structure.
```
├── conf.d
│   ├── auth
│   │   └── htpasswd
│   └── cert
│       ├── fullchain.pem
│       └── privkey.pem
├── nginx.conf
├── ...
```

**2. Deploy the configuration**

Open your nginx configuration file(*var/nginx/nginx.conf*) and paste the following snippet into it.
```
events {
    worker_connections  1024;
}
http {
  upstream docker-registry {
    server localhost:5000;
  }
  ## Set a variable to help us decide if we need to add the
  ## 'Docker-Distribution-Api-Version' header.
  ## The registry always sets this header.
  ## In the case of nginx performing auth, the header is unset
  ## since nginx is auth-ing before proxying.
  map $upstream_http_docker_distribution_api_version $docker_distribution_api_version {
    '' 'registry/2.0';
  }
  server {
    listen 443 ssl;
    server_name YOURDOMAIN;

    # SSL
    ssl_certificate /etc/nginx/conf.d/cert/fullchain.pem;
    ssl_certificate_key /etc/nginx/conf.d/cert/privkey.pem;

    # Recommendations from https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html
    ssl_protocols TLSv1.1 TLSv1.2;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;

    # disable any limits to avoid HTTP 413 for large image uploads
    client_max_body_size 0;

    # required to avoid HTTP 411: see Issue #1486 (https://github.com/moby/moby/issues/1486)
    chunked_transfer_encoding on;

    location /v2/ {
      # Do not allow connections from docker 1.5 and earlier
      # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
      if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
        return 404;
      }

      # To add basic authentication to v2 use auth_basic setting.
      auth_basic "Registry realm";
      auth_basic_user_file /etc/nginx/conf.d/auth/htpasswd;

      ## If $docker_distribution_api_version is empty, the header is not added.
      ## See the map directive above where this variable is defined.
      add_header 'Docker-Distribution-Api-Version' $docker_distribution_api_version always;

      proxy_pass                          http://docker-registry;
      proxy_set_header  Host              $http_host;   # required for docker client's sake
      proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
      proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header  X-Forwarded-Proto $scheme;
    }
  }
}
```
Replace the `YOURDOMAIN` with your domain name. The `ssl_certificate` and `ssl_certificate_key` specify your certificate file path and private key. And the `auth_basic_user_file` specifies your authentication file path. `server localhost:5000` represent that listen on IP address ***localhost*** and port ***5000***, which should be your registry server address.

Use the following snippet to test whether your configuration is correct. 
```
$ nginx -t -c /etc/nginx/nginx.config
```

If you want to track the errors, open your error log. Error log locate at `var/log/nginx/error.log` by default, and access log locate at `var/log/nginx/access.log` by default.
```
$ tail -f /var/log/nginx/error.log
```

**3. Firewall block the registry's port**

As we use an Nginx sit in front of our registry, and we also verify the user's authority based on Nginx authentication mechanism. So, a critical issue is that the user may bypass the domain, directly access the IP and port address instead. As we don't have any authentication protected over the registry, and hence it should be forbidden or redirect the IP traffic on the registry port(in this example, it is `5000`). I am running this example on Debian, and I choose to use the [UFW][5] for achieving the firewall block on the 5000 port. As UFW and Docker have conflicts in [iptables][6], so UFW and Docker may not work well together. To make it work, we have to change some configurations on UFW or Docker demon. For more information, please follow the [Ubuntu - ufw firewall not working at all][3] and [UFW firewall is not working on Ubuntu in DigitalOcean][4].

Install ufw on debian or stop the ufw unity(if you've the ufw installed already).
```
$ # sudo apt-get update && sudo apt-get install ufw
$ sudo ufw disable
```

As UFW and Docker have conflicts in iptables, so UFW and Docker may not work well together. To make the ufw works well, you need add these lines at the end of `/etc/ufw/after.rules`, or add `DOCKER_OPTS="--iptables=false"` on Docker deamon configuration file. For more information, look at the [UFW firewall is not working on Ubuntu in DigitalOcean][4].
```
# BEGIN UFW AND DOCKER
*filter
:ufw-user-forward - [0:0]
:DOCKER-USER - [0:0]
-A DOCKER-USER -j RETURN -s 10.0.0.0/8
-A DOCKER-USER -j RETURN -s 172.16.0.0/12
-A DOCKER-USER -j RETURN -s 192.168.0.0/16

-A DOCKER-USER -p udp -m udp --sport 53 --dport 1024:65535 -j RETURN

-A DOCKER-USER -j ufw-user-forward

-A DOCKER-USER -j DROP -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 192.168.0.0/16
-A DOCKER-USER -j DROP -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 10.0.0.0/8
-A DOCKER-USER -j DROP -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 172.16.0.0/12
-A DOCKER-USER -j DROP -p udp -m udp --dport 0:32767 -d 192.168.0.0/16
-A DOCKER-USER -j DROP -p udp -m udp --dport 0:32767 -d 10.0.0.0/8
-A DOCKER-USER -j DROP -p udp -m udp --dport 0:32767 -d 172.16.0.0/12

-A DOCKER-USER -j RETURN
COMMIT
# END UFW AND DOCKER
```

Set up a firewall, and block the port ***5000***.
```
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
$ # enable ssh(the 22, the default ssh port), 80, 443 and other port you want
$ sudo ufw allow 22/tcp
$ sudo ufw allow 80/tcp
$ sudo ufw allow 443/tcp
$ # block the port 5000, because we don't want client directly access the IP:5000 connect to our registry.
$ sudo ufw deny 5000/tcp
```

When you finish your set up, then enable your ufw.
```
$ sudo ufw enable
```

When all configurations set up correctly, then you can enjoy your registry.
```
$ docker pull hello-world
$ docker login yourdomain:port
$ docker tag hello-world yourdomain:port/hello-world
$ docker push yourdomain:port/hello-world
$ docker logout
```

# Conclusion
The whole set up seems a little complex, but I separate them in detailed steps. And I made detailed explanations of each step to make it simple. You should follow me step by step, and if you got errors, please debug them separately to locate the issues. 
I have summed them up in the following order.
1. Run your registry up on port 5000(or other port you like).
2. Install the Nginx software(You can use the Nginx image, then you don't have to install Nginx software in your local machine.).
3. Deploy the Nginx configurations for reverse proxy, SSL, and basic authentication(You can tail the error log, if you have set up problems `tail -f /var/log/nginx/error.log`).
4. Block the 5000 port to avoid abused access on IP.(I use ufw in this example, but you can choose whatever else firewall software you like.)

if you have any questions or suggestions, please be free to leave comments.

[1]: https://docs.docker.com/compose/
[2]: /docker/2020/09/21/secure-your-registry-with-ssl/
[3]: https://www.digitalocean.com/community/questions/ubuntu-ufw-firewall-not-working-at-all
[4]: https://stackoverflow.com/questions/49549834/ufw-firewall-is-not-working-on-ubuntu-in-digitalocean/49563279#49563279
[5]: https://wiki.ubuntu.com/UncomplicatedFirewall
[6]: https://en.wikipedia.org/wiki/Iptables