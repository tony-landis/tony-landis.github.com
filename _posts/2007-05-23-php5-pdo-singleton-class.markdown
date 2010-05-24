--- 
layout: posts
categories: php
title: PHP5 PDO Singleton Class
tags: php pdo zend
---
PHP5 PDO Singleton Class
========================

I <strike>use</strike> used to use Zend Studio for Eclipse as my PHP IDE. The framework I am developing uses PHP PDO for the database abstraction, and rather than passing around a reference to my PDO instance I wanted to create a singleton.

At the same time, I wanted to take advantage of the helpful code completion and suggestion  features of the Zend IDE. 
So I created a class that creates a PDO singleton object in a way that Zend Studio for Eclipse can still do code assistance.

<strong>PHP5 PDO Singleton Usage Example</strong>

<pre><code>$dbObj =&amp; new sdb("mysql:host=localhost;dbname=testdb", 'username', 'password');</code></pre>


This create a connection to the mysql database 'testdb' and a new PDO instance. 
<code>$dbOjb</code> can be passed around by reference but using the <code>sdb::singleton</code> 
is recommended as Zend will do code assist on this.

Here is the code:

<script src="http://gist.github.com/31464.js"></script>
