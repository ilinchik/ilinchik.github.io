---
layout: post
title: "WebApplicationInitializer implementations"
date: 2015-02-24 01:52:07 +0300
comments: true
published: false
categories: 
- jee
---

Spring WebApplicationInitializer provides a programatic way to configure the Spring DispatcherServlet and ContextLoaderListener in Servlet 3.0+ compliant servlet containers. But how does it work? How servlet container finds WebApplicationInitializer implementations, is it really load all classes from classpath?
