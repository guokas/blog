---
layout: post
title: "Manage your Images, Containers"
date: 2020-07-08 10:03:01+0800
categories:
  - Docker
tags:
navi-enable-docker: true
navi-name: 'Manage your Image, Container'
navi-order: 'a1-2-2'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: After you build and run your image successfully, then you need to manage them. Docker provides a full, complete manage command that fits for you. This article divides into three main parts.It's important to manage your Images in Docker. Docker provides a set of built-in commands that assistant your management on Images.
excerpt: After you build and run your image successfully, then you need to manage them. Docker provides a full, complete manage command that fits for you. This article divides into three main parts.It's important to manage your Images in Docker. Docker provides a set of built-in commands that assistant your management on Images.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-docker %}
        {% if post.navi-order == "a1" or
              post.navi-order == "a1-2"
        %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

After you build and run your image successfully, then you need to manage them. Docker provides a full, complete manage command that fits for you. This article divides into three main parts.
1. Image management.
2. Container management.
3. Interaction.

This blog continues from the previous episode, if you are unfamiliar, take a review look at [build and run your image][1].

# Image Management
It's important to manage your Images in Docker. Docker provides a set of built-in commands that assistant your management on Images. The following contents list the almost highly touched commands during Images management. You can run the `docker image --help` or `docker --help` to explore more information.

**List images**
```
docker image ls
docker images
```

**Remove an image**
```
docker image rm <myimage>
docker rmi <myimage>
```

**Remove an image by force**
```
docker image --force rm <myimage>
docker rmi --force <myimage>
```

**Show an image history**
```
docker image history <myimage>
docker history <myimage>
```

**copy an image from another Image**
```
docker image tag <source_image> <new_image>
```

**Remove unused images**
```
docker image prune
```

**Pull an image or a repository from a gegistry**
```
docker image pull <image_registry>
docker pull <image_registry>
```

**Push an image or a repository to a registry**
```
docker image push <myimage>
docker push <myimage>
```

**Load an image from a tar archive or STDIN**
```
docker image load -i <tarfile>
docker load -i <tarfile>
```

**Save one or more images to a tar arhchive(stream to STDOUT by default)**
```
docker image save -o <output_tar_file>
docker save -o <output_tar_file>
```

<script src="https://gist.github.com/voltwu/873862bb48acfb48e87455a7419f5e5f.js"></script>

# Container Management
You've known how to manage your Images in the command line, where you can also manage your container. Here, I am going to show you the basic commands that you may need. You can run the `docker container --help` or `docker --help` to explore more information.

**List all running containers**
```
docker container ls
docker ps
```
**List all running and stopped containers**
```
docker container ls -a
docker ps -a
```
**List all stopped containers**
```
docker ps --filter "status=exited"
docker ps -f "status=exited"
```

**Stop one or more running containers**
```
docker container stop <mycontainer>
docker stop <mycontainer>
```
**Start one or more stopped containers**
```
docker container start <mycontainer>
docker start <mycontainer>
```

**Remove a container**
```
docker container rm <mycontainer>
docker rm <mycontainer>
```
**Remove a container by force**
```
docker container rm --force <mycontainer>
docker rm --force <mycontainer>
```
**Remove all stopped containers**
```
docker container prune
```

**Copy files/folders between a container and the local filesystem**
```
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH
docker cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH
```
The former copies the container to the host, and the later copies the host to the container.

# Interaction

**Attach local standard input, output, and error streams to a running container**
```
docker attach <mycontainer>
```

**Get inside a container's filesystem on a running container**
```
docker exec -it <mycontainer> bash
```

**Get inside an image's filesystem**
```
docker run -it --entrypoint /bin/bash <imageid>
```

`Docker Attach` and `Docker Exec` both can let you connect to your container, but the `Docker Attach` isn't really the same thing as `SSH`.  For example, if your container is running a webserver, the `Docker Attach` will probably connect you to the *stdout* of the webserver process.  The `Docker Exec` command can let you get inside a running container, it didn't like the `Docker Attach`, `Docker Exec` will let you get inside the really private filesystem. The  `docker run -it --entrypoint /bin/bash <imageid>` base on an image, so you can inspect an image's filesystem, without running it as a container first. If you want to exit the interactive window, input the `exit` command.

If you execute the above `bin/bash` command, you may encounter a crash error. Just as the fallowing:
```CMD
$ # try to get inside on a container
$ docker exec -it <mycontainer> bash
rpc error: code = 2 desc = oci runtime error: exec failed: container_linux.go:247: starting container process caused "exec: \"bash\": executable file not found in $PATH"
$ 
$ # try to get inside on an image
$ docker run -it --entrypoint /bin/bash <myimage>
Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"/bin/bash\": stat /bin/bash: no such file or directory": unknown.
```
You have these errors because your container/image doesn't have a `bash` command installed. However, there are still have other solutions for these problems, if your `bash` hasn't installed, you should use `/bin/sh` instead.

```
$ # try to get inside on a container
$ docker exec -it <mycontainer> /bin/sh
$ 
$ # try to get inside on an image
$ docker run -it --entrypoint /bin/sh <myimage>
```
Or more visualization, you can view your containers filesystem without really get inside it.
```
$ docker exec -ti <mycontainer> ls /etc
```

# Copy files out from image
According to the previous phrase, you already know how to get inside the image. but Analyzing files inside images is not every convenient, so you need to copy them out. use the following snippet:
```
id=$(docker create image-name)
docker cp $id:path - > local-tar-file
docker rm -v $id
```
Change the `path` accordingly, this is done by `docker cp`. you can use other methods, check [Docker - how can I copy a file from an image to a host?][2] for more details.

and the generated `local-tar-file` file is a tar zip file, you can unzip into the current folder with:
```
tar -xvf local-tar-file
```



[1]: /docker/2020/07/08/build-and-run-your-image
[2]: https://stackoverflow.com/questions/25292198/docker-how-can-i-copy-a-file-from-an-image-to-a-host
