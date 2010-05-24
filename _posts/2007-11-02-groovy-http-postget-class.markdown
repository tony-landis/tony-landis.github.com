--- 
layout: posts
categories: code
title: Groovy Http POST/GET Class
tags: groovy
---

Groovy Http POST/GET Class
==========================

I was working on a project in <a href="http://groovy.codehaus.org/" target="_blank">Groovy</a> and needed to do POST as well as read the response headers.

I was unable to find a suitable class or method for this in either Groovy or Java, so I wrote this class. 

It opens a socket to the requested host, and writes to the socket using <code>getOutputStream()</code> and <code>PrintWriter()</code>. 

It reads the response from the server using <code>DataInputStream()</code> and <code>getInputStream()</code>.

<script src="http://gist.github.com/31445.js"></script>
