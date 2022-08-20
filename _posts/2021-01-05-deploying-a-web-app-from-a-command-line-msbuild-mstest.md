---
layout: post
title: "Deploying a Web App from a Command Line using MSBuild, MSTest, and WebDeploy"
date: 2021-01-05 09:03:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Deploying a Web App from a Command Line using MSBuild, MSTest, and WebDeploy'
navi-order: 'a1-9-2-1'
toc: true
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: The Microsoft Build Engine(aka. MSBuild) is a platform for building applications. You can define an XML file, which guilds how the MSBuild behavior. The Visual Studio uses MSBuild to achieve the building process. However, the MSBuild doesn't depend on Visual Studio, and hence you can invoke the MSBuild.exe without Visual Studio IDE installed. MSTest is a command-line command to run tests. Web Deploy (aka. msdeploy) is a command-line executable tool created by Microsoft, which simplifies the deployment of Web applications and Web sites to IIS servers.
excerpt: The Microsoft Build Engine(aka. MSBuild) is a platform for building applications. You can define an XML file, which guilds how the MSBuild behavior. The Visual Studio uses MSBuild to achieve the building process. However, the MSBuild doesn't depend on Visual Studio, and hence you can invoke the MSBuild.exe without Visual Studio IDE installed. MSTest is a command-line command to run tests. Web Deploy (aka. msdeploy) is a command-line executable tool created by Microsoft, which simplifies the deployment of Web applications and Web sites to IIS servers.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% assign number = page.navi-order | split: post.navi-order | size %}
        {% if number == 2 %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

In this article, I will guild you build and deploy a web application by using MSBuild, MSTest, and MSDeploy. 

**MSBuild**: The Microsoft Build Engine(aka. MSBuild) is a platform for building applications. You can define an XML file, which guilds how the MSBuild behavior. The Visual Studio uses MSBuild to achieve the building process. However, the MSBuild doesn't depend on Visual Studio, and hence you can invoke the MSBuild.exe without Visual Studio IDE installed.

**MSTest**: MSTest is a command-line command to run tests. 

**MSDeploy**: Web Deploy (aka. msdeploy) is a command-line executable tool created by Microsoft, which simplifies the deployment of Web applications and Web sites to IIS servers.

<blockquote>
<b>NOTE:</b>

MSBuild, MSTest, and MSDeploy tools are only for Microsoft platforms(<b>Windows</b> Operation System). And it is not compatible with non-Microsoft platforms such as Linux or Mac.
</blockquote>

I created a simple *.net core web application* for this demonstration. Project File Structures:

<pre>
├─<bold>UnitTestProject</bold>
│  ├─...
│  └─UnitTest.cs
├─<bold>WebApplication</bold>
│   ├─Controllers
│     └─...
│   ├─Views
│     └─...
│   └─wwwroot
│     └─...
├─<bold>WebApplication.sln</bold>
</pre>

I also created an IIS server, which will be used later for the deployment part. 
<div class="imgcenter" markdown="1">
![Alt][1]*IIS Web Application*
</div>
As you can see from the above, I created an IIS server for demonstrating, which has the physical path `D:\webapplication` on my laptop. Currently, the `D:\webapplication` folder is empty, which will be filled by using `MSDeploy.exe` later.

# Build by using MSBuild
To get started, you need to install the `MSBuild.exe` program on your machine. For me, I have a Visual Studio 2019 installed on my machine, and the location of my MSBuild is <green>C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\msbuild.exe</green>. However, the MSBuild can be installed without installing visual studio IDE, take a look at [Getting msbuild.exe without installing Visual Studio][2] for more details.


Navigate into the project's directory.
```
> dir
2021/01/05  22:22    <DIR>          UnitTestProject
2021/01/05  22:21    <DIR>          WebApplication
2021/01/05  22:22             1,647 WebApplication.sln
```

<br/>
**Clean**, it removes the code that previous built.
<pre>
<green>"C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\msbuild.exe"</green> /p:Configuration=Release <bold>/t:Clean</bold> WebApplication.sln
</pre>

<br/>
**Restore**, downloads and installs any packages missing.
<pre>
 <green>"C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\msbuild.exe"</green> /p:Configuration=Release <bold>/t:restore</bold> WebApplication.sln
</pre>

<br/>
**Build**, build this project to deploy ready.
<pre>
<green>"C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\msbuild.exe"</green>  /p:Configuration=Release <bold>/P:DeployOnBuild=true</bold>  WebApplication.sln
</pre>

This build will generate your codes for deployment in `bin\Debug\netcoreapp3.1` under each project. `/p:Configuration=Release` instructs MSBuild to build in a release mode.

By the way, the above command may do not copy your `Views` and `wwwroot` into your publish directory for the web application. However, you can define copy rules in the `WebApplication.csproj` file. For more information, take a look at [Microsoft Doc MSBuild][3]. Here, I use `xcopy` command to copy `Views` and `wwwroot` into my publish directory.

<pre>
<bold>xcopy</bold> /E /I /Y WebApplication\<bold>wwwroot</bold> WebApplication\bin\Release\netcoreapp3.1\Publish\wwwroot
<bold>xcopy</bold> /E /I /Y WebApplication\<bold>Views</bold> WebApplication\bin\Release\netcoreapp3.1\Publish\Views
</pre>

# Test by using MSTest
You need to install MSTest on your machine first, as we need to use MSTest later. However, the MSTest is a bit hard to install without Visual Studio IDE installed. Take a look at [Can I use mstest.exe without installing Visual Studio?][4] for more details.

For me, I have Visual Studio 2019 installed, so I can find the `mstest.exe` in <green>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\Common7\IDE\MSTest.exe</green> on my local machine.
<pre>
<green>"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\Common7\IDE\MSTest.exe"</green> /testcontainer:"UnitTestProject\bin\Release\netcoreapp3.1\UnitTestProject.dll" /detail:testtype
</pre>

You may encounter this problem `No tests to execute.`, because the MSTest locks compatibilities for the CLI environment. If it doesn't work, you really should consider using `dotnet test` or `vstest.console.exe` instead. Take a look at [MStest.exe via CLI does no work][5] for more details.

Example `vstest.console.exe` for test on my machine:
<pre>
<green>"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\Common7\IDE\Extensions\TestPlatform\vstest.console.exe"</green>  "UnitTestProject\bin\Release\netcoreapp3.1\UnitTestProject.dll"
</pre>


# Deploy by using Web Deploy
Web Deploy(aka. MSDeploy) is a tool that makes your IIS deployment easier and faster.  At present, the latest version is `3.6`, which compatible with IIS 7, IIS 7.5, IIS 8, IIS 8.5, IIS 10. 

You can download Web Deploy from [IIS Download Web Deploy][6] website, once you installed, find the `MSDeploy.exe`'s location. For me, it is <green>C:\Program Files (x86)\IIS\Microsoft Web Deploy V3\msdeploy.exe</green> . By the way, Web Deploy has nothing to do with Visual Studio, they don't depend on each other. 


**Prepare source package**. The MSBuild takes the zip archived file as its source package, so we need to achieve our published web files as a zip file.
```
WinRAR.exe a -afzip -ep1 -r "WebApplication\bin\Release\netcoreapp3.1\Publish\WebApplication.zip" "WebApplication\bin\Release\netcoreapp3.1\Publish\"
```

The above command will archive all files under `WebApplication\bin\Release\netcoreapp3.1\Publish\` in a zip file named `WebApplication.zip`, which located in the same `Publish` folder.
<br/>

**Grant permissions**
In order to run `Msdeploy.exe` normally, the current user need to have the following permissions:

1. Read permission to `%windir%\system32\inetsrv\config`.
2. Modify permission to `%windir%\system32\inetsrv\config\applicationHost.config`.

If you want to get know more about this, take a look at [Error when you use the Web Deployment tool as a non-administrative user][7] for more details.
<br/>

**Deploy to the local IIS server**
<pre>
<green>"C:\Program Files (x86)\IIS\Microsoft Web Deploy V3\msdeploy.exe"</green> -verb:sync <bold>-source:package</bold>="WebApplication\bin\Release\netcoreapp3.1\publish\WebApplication.zip" <bold>-dest:contentPath</bold>='WebApplication'
</pre>

The above command uses `WebApplication\bin\Release\netcoreapp3.1\publish\WebApplication.zip` as a source package, and deploy this package to the `WebApplication` website in IIS. 

The `-dest:contentPath='WebApplication'` informs `msdeploy.exe` deploy to the `WebApplication` website, you can change the `WebApplication` to your other web application by specifying your website name in the `contentPath` field `-dest:contentPath='Your Website Name'`.

# Conclusion
In this article, we have discussed how to build, test, and deploy on the Windows platform. The `MSBuild` and `MSDeploy` are both independent tools. However, It is hard to set up an `MSTest` without Visual Studio installed(you need to configure many dependencies), and also `MSTest` is less compatible in the Windows CLI environment. You really should consider for testing using `dotnet test` or `vstest.console.exe` instead.


[1]: /public/img/2021-01-05-deploying-a-web-app-from-a-command-line-msbuild-mstest-a.png
[2]: https://stackoverflow.com/questions/25506178/getting-msbuild-exe-without-installing-visual-studio
[3]: https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild
[4]: https://stackoverflow.com/questions/3402899/can-i-use-mstest-exe-without-installing-visual-studio
[5]: https://github.com/Microsoft/vstest/issues/1213
[6]: https://www.iis.net/downloads/microsoft/web-deploy
[7]: https://docs.microsoft.com/th-th/troubleshoot/iis/web-deploy-error-non-admin-user