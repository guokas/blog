---
layout: post
title: "How to use Jetty build simple Http Server"
date: 2019-08-29 10:01:01+0800
tags:
  - Java
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
comments: true
description: use Java Jetty builds Server. Jetty can build a server quickly within steps.it wipes extra procedures.
excerpt: use Java Jetty builds Server. Jetty can build a server quickly within steps.it wipes extra procedures.
---
Hello guys. I would like to explain how to use Jetty to build HTTP server in this blog. Welcome to leave comments if you have any troubles or thoughts about it.

# Precondition

Readers should download Jetty before the starting. According to official documents, readers can download Jetty at [the download page][1], and access API document



# Build HTTP Jetty server
The first step, create a server instance
```java
Server server = new Server(8081);
```
Create a basic jetty server object that will listen on port 8081. Note that if you set this to port 0 then a randomly available port will be assigned that you can either look in the logs for the port or programmatically obtain it for use in test cases.



The second step, need to set Handler which should be wrapped.
```java
server.setHandler(handler);
```
the handler is an instance of an interface implementation class of `org.eclipse.jetty.server.Handler` interface. A Handler instance is required by an `org.eclipse.jetty.server.Server` to handle incoming HTTP requests.

`Handlers` are passed the servlet API request and response object but are not `Servlets`. The servlet container is implemented by handlers for context, security, session, and servlet that modify the request object before passing it to the next stage of handling.

`org.eclipse.jetty.server.Handler` interface has lots of derived class, this chapter will demonstrate `ServletContextHandler` usage.


`handler` has a relevance with `Servlet` Instance by function `handler.addServlet()`.
```java
  ServletContextHandler handler = new ServletContextHandler(
  	ServletContextHandler.SESSIONS|ServletContextHandler.NO_SECURITY);
  handler.setContextPath("/hello");
  ServletHolder myserverletholder = new ServerletHolder(new myserverlet());
  handler.addServlet(myserverletholder, pathSpec);
```
`myservletholder` is an instance of `org.eclipse.jetty.servlet.ServletHolder`. it's a servlet instance and context holder. Holds the name, params and some state of a `javax.servlet.Servlet` instance. It implements the `ServletConfig` interface. This class will organize the loading of servlets when needed or requested.



Each `ServletHolder` instance normally interests a `javax.servlet.Servlet` interface. servlet is a small Java program that runs within a Web server. servlets receive and respond to requests from Web clients, usually across HTTP(HyperText Transfer Protocol). To implement this interface,  you can write an HTTP servlet that extends to `javax.servlet.http.HttpServlet`.


```java
    public static class myservlet extends HttpServlet {
        /**
         * handle get request
         */
        @Override
        protected void doGet(HttpServletRequest request,
                HttpServletResponse response) throws ServletException,
                IOException {
        //somecode
        }
        /**
         * handle post request
         */
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
         //somecode
        }
```

The last step, need to start the server
```java
server.start();
```
We have discussed the `ServletContextHandler` usage, actually `org.eclipse.jetty.server.Handler` interface has lot of derived classes.  The `ServletContextHandler` is only one of the `org.eclipse.jetty.server.Handler`'s drived classes. And the `ServletContextHandler` is not suitable for handling more complicated scenarios, my GitHub had demonstrated [WebAppContext extends to org.eclipse.jetty.server.Handler][WebAppContext] demo.

# Epilogue
Above demonstrated how to create a simple HTTP server use Jetty, I hope this page will be helpful for you. if you have any troubles or thoughts, welcome to leave messages. Have a good day!

[WebAppContext]: https://github.com/voltWu/Jetty-HTTP-example
[1]: https://www.eclipse.org/jetty/download.html
{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://https-github-com-voltwu-blog.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
