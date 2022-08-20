---
layout: post
title: "Response Compression"
date: 2020-06-24 09:04:01+0800
categories:
  - CSharp-ASP.NET Core
tags:
navi-enable-csharpaspnetcore: true
navi-name: 'Response Compression'
navi-order: 'a1-8-1'
toc: false
toc_label: "TABLE OF CONTENTS"
toc_icon: "cog"
comments: true
description: How to compress your response contents. How to compress image or html, or other resource.
excerpt: How to compress your response contents. How to compress image or html, or other resource.
---
<!--navigation bar-->
<div class='navi-link-container'>
  {% assign posts = site.posts|sort:'navi-order' %}
  {% for post in posts %}
    {% if post.navi-enable-csharpaspnetcore %}
        {% if post.navi-order == "a1" or 
              post.navi-order == "a1-8" %}
            <a href="{{ site.baseurl }}{{ post.url }}" class='navi-link'>{{post.navi-name}}</a>
        {%endif%}
    {% endif %}
  {% endfor %}
<a class='navi-link' href="">{{page.navi-name}}</a>
</div>
<!--navigation bar-->

Asp.Net Core provides a response compression middleware, which used to reduce response size to improve responsiveness.

Use response middleware when you are:
1. Unable to use the [IIS Dynamic Compression module][1], [Apache mod_deflate module][2], or [Nginx Compression and Decompression][3] because of those have a built-in response compression support.
2. Response size smaller than about 150-1000 bytes(depending on the file's content and the efficiency of compression). The overhead of compressing small files may produce a compressed file larger than the uncompressed file about 150-1000 bytes.
3. Compress MIME types not belong to the default compression MIME types, like image/svg+xml. 

The following code shows how to use response compression middleware in the ***Startup*** file
```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseResponseCompression();
}
```
Use the `UseResponseCompression` method to achieve response compression. As our previous article mentioned, the execution sequence is critical. So, the `app.UseResponseCompression` must be called before any middleware that compresses responses.

In most cases, a website contains a **static file serving short-circuiting** middleware and an endpoint middleware. We need to be careful that the position of response compression should be before any of them. Like the following code illustrates:
```c#
public void Configure(IApplicationBuilder app, 
    IWebHostEnvironment env)
{
    app.UseResponseCompression();

    app.UseStaticFiles();

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

You can specify a response compression service by calling the `AddResponseCompression` extension method in `ConfigureServices`, which accepts a `Action<ResponseCompressionOptions>` parameter. 
```c#
services.AddResponseCompression(options => {
    options.Providers.Add<BrotliCompressionProvider>();
    options.Providers.Add<GzipCompressionProvider>();
    options.MimeTypes =
        ResponseCompressionDefaults.MimeTypes.Concat(
            new[] { "image/jpeg", "image/png", "application/font-woff2", "image/svg+xml" });
    options.EnableForHttps = true;
});
```
The `BrotliCompressionProvider` is for **Brotli** encoding, and the `GzipCompressionProvider` is for **Gzip** encoding. You can even provide your custom compression provider, which needs to implement the `ICompressionProvider`.

It's not all MIMEs are supported for default response compression.  The following table illustrates a default set of MIME types for compression.

|MIME   |
|:-:|
|application/javascript   |
|application/json   |
|application/xml   |
|text/css   |
|text/html   |
|text/json   |
|text/plain   |
|text/xml   |

We can note that the above list mimes don't contain image related MIME types. We can replace or append with the Response Compression Middleware options to make it support our custom MIME types. Note that wildcard MIME types, such as `text/*` aren't supported. 
```c#
//Concat our custom MIME types to the default mime
options.MimeTypes =
    ResponseCompressionDefaults.MimeTypes.Concat(
        new[] { "image/jpeg", "image/png", "application/font-woff2", "image/svg+xml" });

//wildcard MIME types aren't supported
// options.MimeTypes =
//     ResponseCompressionDefaults.MimeTypes.Concat(
//         new[] { "image/*" });
```
By default, the compression doesn't support the Https connection. If you want to the response over HTTPS connection should be compression, then assign with `true` value.
```c#
options.EnableForHttps = true;
```

Thanks for reading this, I hope this article can help you deal with your problems. Happy Dragon Boat Festive.

[1]: https://www.iis.net/overview/reliability/dynamiccachingandcompression
[2]: https://httpd.apache.org/docs/current/mod/mod_deflate.html
[3]: https://docs.nginx.com/nginx/admin-guide/web-server/compression/
