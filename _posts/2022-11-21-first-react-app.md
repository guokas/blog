---
layout: post
title: "First React App"
date: 2022-11-18 10:01:01+0800
categories:
tags:
  - React
navi-name: 'First React App'
navi-order:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: First React App
excerpt: First React App
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

Today, I will create a react web application with react standards creating command. But, I will introduce some acknowledges that keep you from inaccessible the standards registry in your country(such as China). 

# Standard Offical Command
The first is first. You see many places that use this command to create react web applications. 
```shell
npx create-react-app my-app
cd my-app
npm start
```

By default, `npx` are slow and will take some times. You are use `yarn` instead.
```shell
npm install --global yarn
yarn --version

yarn global add create-react-app
create-react-app my-app
```

# Use Custom Registry
If you got an error that indicates that this registry URL is inaccessible, then you need to switch to an accessible registry. If you live in China, you can use this registry.`registry=https://registry.npm.taobao.org/`.  Or you can change the command manually in your `.npmrc` file. I recommend to change variables to command:
```shell
npm config set registry=https://registry.npm.taobao.org
npm config get registry

npm config list

npm install -g create-react-app

create-react-app my-app
cd my-app
npm start
```

# Use Private Registry

If you are using a private registry. The only thing you need to be concerned about is changing the registry and setup credentials.
```shell
npm config set registry=https://your private registry
npm config set _auth="<token>"
```

The command is encrypted by base64
```shell
echo -n "<lowecase_email>:<password>" | openssl base64
```

If you don't have OpenSSL installed on Windows, you can use the following snippet in windows Powershell.
```shell
$MYTEXT = '<lowecase_email>:<password>'
$ENCODED = [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($MYTEXT))
Write-Output $ENCODED
```
<blockquote>
<h2>Note:</h2>
You can also google to encrypt online base64 credentials, You can also decrypt your credentials, sometimes, windows or Linux will append a newline character <code>\n</code> or <code>\r\n</code> at the end, make sure that doesn't include in your passport.
</blockquote>

# .npmrc config file
The `.npmrc` file can exist in four different places.

| npmrc file | Path |
| --- | ----------- |
| Per-project NPM config | projectPath\\. npmrc |
| Global NPM config | C:\Users\%username%\AppData\Roaming\npm\etc\\.npmrc |
| Per-user NPM config | C:\Users\%username%\\.npmrc |
| Built-in NPM config | nodejsPath\node_modules\npm\\.npmrc |


` Per project NPM config` and `Per-user NPM config` are both frequently used config files. Publishable settings can be placed under `Per-Project`, sensitive information should be placed under `Per-user` folder.

# Cache
The npm cache file located at your `C:\Users\{User}\AppData\Local\npm-cache` and `C:\Users\{User}\AppData\Roaming\npm-cache` folders. You can clean your cache by deleted these two folders, or with command
```shell
npm cache clean --force 
```
