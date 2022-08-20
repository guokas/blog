---
layout: post
title: "Set up a docker registry on debian 4.6"
date: 2020-09-10 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Set up a docker registry on debian 4.6'
navi-order: 'a1-4-2-1'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: This article will lead you to set up a private registry step by step. It is pretty easy to set a registry up with a public registry image in Docker Hub. At the end of this lesson, you may ask more questions, such as how to secure your registry, how to distribute roles. All those advanced fundamentals I will discuss in the next articles, this one is only focusing on how to running up a registry and manage its repositories.
excerpt: This article will lead you to set up a private registry step by step. It is pretty easy to set a registry up with a public registry image in Docker Hub. At the end of this lesson, you may ask more questions, such as how to secure your registry, how to distribute roles. All those advanced fundamentals I will discuss in the next articles, this one is only focusing on how to running up a registry and manage its repositories.
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
This article will lead you to set up a registry step by step. It is pretty easy to set a registry up with a public registry image in Docker Hub. At the end of this lesson, you may ask more questions, such as how to secure your registry, how to distribute roles. All those advanced fundamentals I will discuss in the next articles, this one is only focusing on how to running up a registry and manage its repositories.

# Warm up
This article uses Docker Hub `registry` repository, which you can view its latest version on [Docker Hub][1]. By the way, at present(the latest version is `2.7.1`), the registry repository only available on the linux platform, and hence be sure you have the right OS. My machine is `Debian 4.9` and illustrates with `registry:2.7`.

# Deploy a registry server
Use the following snippet to run a registry server up.
```
$ docker run -d -p 5005:5000 --restart always --name registry registry:2.7
```
The above code pulls the `registry:2.7` image from the docker hub and runs it as a container, which explored port 5000 to match port 5005 of the host.  `--restart always` instructs the container to restart always when unexpected stops occur.

As the opposite of launch from the raw command, you can use a [compose][2] file instead. Put the following snippet into your `docker-compose.yml` file.
```
registry:
  restart: always
  image: registry:2.7
  ports:
    - 5005:5000
  container_name: registry
```
Then start up your application in the background by running `docker-compose up -d`.

Now, a registry container is successfully running on your device. You can view it with the `docker ps` command.
```
$ docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
b90413c663ae        registry:2.7            "/entrypoint.sh /etc…"   4 hours ago         Up 4 hours          0.0.0.0:5005->5000/tcp   registry
```

# Push/Pull Images 
You can access your docker registry from either localhost or other address. The way that you access your registry is the same way that you do on the docker hub. If you are don't remember, you can review [Share Your Image On Docker Hub][3] for more syntax details.

**Access from localhost:**
First, let us access from the localhost. Here, take the example with an `ubuntu` image.
```
$ # pull a ubuntu image from docker hub
$ docker pull unbuntu
Using default tag: latest
latest: Pulling from library/ubuntu
e6ca3592b144: Pull complete 
534a5505201d: Pull complete 
990916bd23bb: Pull complete 
Digest: sha256:cbcf86d7781dbb3a6aa2bcea25403f6b0b443e20b9959165cf52d2cc9608e4b9
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
$ 
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              4e2eef94cd6b        3 weeks ago         73.9MB
$ 
$ # tag your ubuntu image into the localhost:5005/my-ubuntu
$ docker tag ubuntu localhost:5005/my-ubuntu
```

Push the `localhost:5005/my-ubuntu` to your registry.
```
$ docker push localhost:5005/my-ubuntu
```

Pull your `localhost:5005/my-ubuntu` image back from your registry.
```
$ docker pull localhost:5005/my-ubuntu
```

The above illustrates how to access the registry from local. However, you probably meet requirements that access from other hosts. 

**Access from other hosts:**
```
$ docker tag ubuntu xxx.xxx.xxx.xxx:port/my-ubuntu
$ 
$ docker push xxx.xxx.xxx.xxx:port/my-ubuntu
$ 
$ docker pull xxx.xxx.xxx.xxx:port/my-ubuntu
```
Replace the `xxx.xxx.xxx.xxx:port` with your `IP` and `PORT`. If you are the first time to push a image from a client to a registry, you may encounter the following error.
```
Get https://xxx.xxx.xxx.xxx:port/v2/: http: server gave HTTP response to HTTPS client
```
It is because the docker daemon has forbidden insecure connection like this. You should configure your daemon properly, open your `/etc/docker/daemon.json`(`settings->daemon` in Docker Desktop) file in the client, and put the following contents into it.
```
{ 
  "insecure-registries":["xxx.xxx.xxx.xxx:port"] 
}
```
Then restart your docker deamon.
```
$ systemctl daemon-reload
$ systemctl restart docker
```
# Images management in registry
Now, you have images stored in your registry server. According to the [official document][5], the registry server uses a [volume][4] for storing by default. 

List all volumes out through the `docker volume ls` command.
```
$ docker volume ls
DRIVER              VOLUME NAME
local               faaf949a2ad2f8ffdef13662ac749c2736fe13be53f11e94fb6779f10f656fd2
```

You may see more than one volume, find the registry one. Once you find it, then inspect its details.
```
$ docker volume inspect faaf949a2ad2f8ffdef13662ac749c2736fe13be53f11e94fb6779f10f656fd2
[
    {
        "CreatedAt": "2020-09-16T13:22:35+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/faaf949a2ad2f8ffdef13662ac749c2736fe13be53f11e94fb6779f10f656fd2/_data",
        "Name": "faaf949a2ad2f8ffdef13662ac749c2736fe13be53f11e94fb6779f10f656fd2",
        "Options": null,
        "Scope": "local"
    }
]
```

Get inside the mount point folder, and list stored images.
```
$ cd /var/lib/docker/volumes/faaf949a2ad2f8ffdef13662ac749c2736fe13be53f11e94fb6779f10f656fd2/_data
$ ls docker/registry/v2/repositories/
my-ubuntu  ubuntu
```
Image and folders are one to one relations, and the folder name is the image's name. In this example, you can see that I have two images stored in this registry, and they are `my-ubuntu` and `ubuntu`. Get inside the folder, you can find more details, such as `layers`, `revisions`, and `tags`.

If you want to delete an image from the registry, the only thing you need to do is that delete the corresponding folder. 
```
$ ls
my-ubuntu  ubuntu
$ rm -rf my-ubuntu
$ ls
ubuntu
```
The above snippet removed the `my-ubuntu` image from the docker registry. 

As the opposite to inspect your volume details for registry storage location, you can get into a registry container for storage information instead. They both have the same result. At the present, your container is already running up, so use the following command to get inside it.
```
$ docker exec -it <mycontainer> /bin/sh
```

Note, a registry container doesn't have `bash` installed by default, so you should use the `sh` instead. 
```
$ docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
d09c04a74c93        registry:2.7            "/entrypoint.sh /etc…"   3 days ago          Up 3 days           0.0.0.0:5005->5000/tcp   registry
$ 
$ docker exec -it d09c04a74c93 /bin/sh
```

The `/var/lib/registry` path stores the repositories.
```
$ docker exec -it d09c04a74c93 /bin/sh
$ 
$ # Now, I am in the registry container.
$ cd /var/lib/registry
$ ls
docker
$ cd docker/registry/v2/repositories/
$ ls
ubuntu
```

If you want to change the default storage location, you might use a bind mount instead. see [Storage Customization][5] for more details.

# Conclusion
Congratulations! You have learned how to set up a basic docker registry and manage its repositories. As malicious hackers can easily leverage vulnerabilities on an open docker registry, so this is only recommended for the test environment. 

In the next, I will write more articles and leads you to secure the registry step by step. Please give me a thumber up, if this article helped you.


[1]: https://hub.docker.com/_/registry
[2]: https://docs.docker.com/compose/gettingstarted/
[3]: https://voltwu.github.io/blog/docker/2020/07/08/share-your-image-on-docker-hub/
[4]: https://docs.docker.com/storage/volumes/
[5]: https://docs.docker.com/registry/deploying/#storage-customization