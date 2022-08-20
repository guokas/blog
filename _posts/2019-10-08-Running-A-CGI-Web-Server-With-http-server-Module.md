---
layout: post
title: "Running A CGI Web Server With http.server Module"
date: 2019-10-08 10:01:01+0800
tags:
  - Python
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
comments: true
description: CGI is Common Gateway Interface, with python build-in http.server module. can quickly and easily demonstrate how to use it. 
excerpt: CGI is Common Gateway Interface, with python build-in http.server module. can quickly and easily demonstrate how to use it. 
---
In this article, I will use `http.server` that python's build-in server module to demonstrate how to use cgi and explain what is cig. my environment is python3. Have a pleasant reading.
# What is CGI
Common Gateway Interface(CGI) is a set of standard protocols to define how data exchanges between webserver and custom scripts. 

Let us think about this scenario:
1. When a hyperlink requesting arrived at webserver. the server will parse the URL and demands(for example: a filename). 
2. The server will look through the corresponding file and return the file content to the requesting client.
3. The requesting client display contents that webserver returned.

The above steps are the process for a static web page. However, the server can be configured to that the requested file in a specified directory does not be sent back, instead it's executed as a program. and whatever the output of the program will be sent back to the browser client. this functionality is called Common Gateway Interface. Common Gateway Interface has laid a foundation for dynamical web pages.

Common Gateway Interface Architecture Diagram

![Alt][1]

# http.server
The `http.server` module defines classes for implementing HTTP servers(web servers). 

According to official documents in python3.7, the `server_address` and `http_handler_class` should be provided when starting up a webserver with `http.server` module.

Below is a simple example:

```python
import http.server
import socketserver

def run(server_class=http.server.HTTPServer, handler_class=http.server.BaseHTTPRequestHandler):
    server_address = ('', 8000)
    with server_class(server_address, handler_class) as httpd:
        httpd.serve_forever()

run(server_class = socketserver.TCPServer, handler_class = http.server.SimpleHTTPRequestHandler)
```
Put the above contents into *index.py* file. in the same directory of *index.py* in command prompt, input the `python index.py` and running. Then the webserver is starting up and serving files relative to the current directory.

However, except starting from code, the server can also start from the command prompt. use the `-m` switch of the interpreter with a *port number* argument.

```python
python -m http.server 8000
```
The webserver serves files relative to the current directory.

All of the above snippets are not relative to the Common Gateway Interface. By default, a lot of web servers do not boot the CGI functionality. the programmer must configure manually it to start. the python has provided the  `http.server.CGIHTTPRequestHandler`  class to handle only the CGI functionality.

```python
#use the above run method
run(server_class = http.server.HTTPServer, handler_class = http.server.CGIHTTPRequestHandler)
```
And the `CGIHTTPRequestHandler` can be enabled in the command line with `--cgi` option.

```python
python -m http.server --cgi 8000
```

# CGI Example
In this chapter, we will demonstrate a simple example that will print "hello world".

**First: create a CGI directory**

create a directory *cgi-bin* in *basepath*. The directory name can be only *cgi-bin* or *htbin*.

**Second: create a python script**

```python
#!/usr/bin/python

# Import modules for CGI handling 
import cgi, cgitb 

# Create instance of FieldStorage 
form = cgi.FieldStorage() 

# Get data from fields
first_name = form.getvalue('first_name')
last_name  = form.getvalue('last_name')

print ("Content-type:text/html\r\n\r\n")
print ("<html>")
print ("<head>")
print ("<title>Hello</title>")
print ("</head>")
print ("<body>")
print ("<h2>Hello World, %s %s</h2>" % (first_name, last_name))
print ("</body>")
print ("</html>")
```
put the above script into the *hello.py* file in directory *basepath/cgi-bin*.

**Third: start-server**

start the server in command prompt in directory *basepath* with below commanding:
```python
python -m http.server --cgi 8000
```

Then access [http://localhost:8000/cgi-bin/hello.py?first_name=jame&last_name=curry](http://localhost:8000/cgi-bin/hello.py?first_name=jame&last_name=curry "link") in the browser client.
![Alt][2]

# Conclusion
CGI is the Common Gateway Interface that defines the standard exchange format between webserver and custom scripts.

With `http.server` module, the programmer can build a web server quickly. a lot of web server does not boot up CGI functionality by default. In python, use the `CGIHTTPRequestHandler` class to handle cig request or `-m` switch of the interpreter in the command line. read more about http.server at [http.server library][3].

Okay, everything is settled up. Today is the first working day after the  7-day national holiday. Have a nice day at work!

[1]: /public/img/2019-10-08-Running-A-CGI-Web-Server-With-http-server-Module-cgi-diagram.png
[2]: /public/img/2019-10-08-Running-A-CGI-Web-Server-With-http-server-Module-a.png
[3]: https://docs.python.org/3/library/http.server.html
