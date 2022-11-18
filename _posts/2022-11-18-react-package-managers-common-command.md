---
layout: post
title: "General React Package Management Commands"
date: 2022-11-18 10:01:01+0800
categories:
tags:
  - React
navi-name: 'General React Package Management Commands'
navi-order:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: General React Package Management Commands
excerpt: General React Package Management Commands
---
<!--navigation bar-->
<!-- 
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
-->
<!--navigation bar-->


Here will demonstrate to you the package management commands. The official standard command is through npm, but some guys use yarn. Both are fine, and frequently used. 

All packages divided into local and global. They are separated. global packages are for general packages, such as `create-react-app`, `yarn`, `sass` etc...

# npm 
install packages
```shell
npm install <package_name>
```

install packages globally
```shell
npm install -g <package_name>
```

list local pacakages
```shell
npm list
```

list global pacakages
```shell
npm list -g
```

# yarn 
install packages
```shell
yarn add <package_name>
```

```shell
yarn global add <package_name>
```

list local pacakages
```shell
npm list
```

list global pacakages
```shell
yarn global list
```