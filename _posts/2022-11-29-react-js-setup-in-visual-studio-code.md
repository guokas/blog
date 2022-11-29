---
layout: post
title: "react js setup environment in visual studio code"
date: 2022-11-29 10:01:01+0800
categories:
tags:
  - React
navi-name: 'react js setup environment in visual studio code'
navi-order:
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: react js setup environment in visual studio code
excerpt: react js setup environment in visual studio code
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

Today, I will introduce how to setup a react js environment within visual studio code in windows. A conducive environment will help expedite your development, make your debugging easier, and produce better quality code.

Our lesson will be divided into two parts. This article discusses the needed react js plugin in the first part, while the second step explains how to set up a debugging environment.

# Install ReactJS Code Snippets On Visual Studio Code

Take a look at [ReactJS Code Snippets Plugin][1] for repository details. 

After you've installed it on your windows visual studio code, you can quickly create a stateless function by typing `rfc`.

<div class="imgcenter" markdown="1">
![Alt][2]*create stateless function*
</div>

They also provide other comprehensive shortcuts. Install it and start exploring.


# React Developer Tools
The web browser plugin will help developers visualize state values, and component hierarchies. This is a handy browser plugin for reactjs.

You can more more information on [Chrome React Developer Tools][3] or [Firefox React Developer Tools][4]

[1]: https://marketplace.visualstudio.com/items?itemName=xabikos.ReactSnippets
[2]: /public/img/2022-11-29-react-js-setup-in-visual-studio-code-a.png
[3]: https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi
[4]: https://addons.mozilla.org/en-GB/firefox/addon/react-devtools/
 