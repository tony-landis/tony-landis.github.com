--- 
layout: posts
categories: php
title: Convert from ADOdb Library to PDO
tags: php adodb pdo
---

Convert from ADOdb Library to PDO
=================================


In the past I used the <a title="php pdo singleton class" href="/php/php5-pdo-singleton-class/">ADOdb Database Abstraction Library</a> for PHP for a lot of my PHP projects. However, PDO (PHP Data Objects) is now a better fit as it performs better for me and doesn't require the inclusion of any libraries.

I wanted to make the switch not only for future projects, but also to go back into some of my existing projects that I maintain and covert from ADOdb to PDO. However, I didn't want to alter thousands of calls to ADOdb methods.

The solution I came up with was to do some more work on my PDO Singleton Class. By adding in the ADOdb methods <code>Execute()</code>, <code>qstr()</code>, <code>GetOne()</code>, and an iterator for the adodb recordsets returned by <code>Execute()</code>, I was able to easily transition to PDO in projects with thousands of lines of code by including this class and creating a new instance of <code>sdb()</code> instead of <code>NewADOConnection()</code> and <code>Connect()</code>.

<pre><code>$DB =&amp; new sdb("mysql:host=localhost;dbname=testdb", 'username', 'password');</code></pre>

This is the equivalent of the following ADOdb code: (except it is a singleton)

<pre><code>$DB = NewADOConnection('mysql');
$DB-&gt;Connect("localhost", "username", "password", "testdb");</code></pre>

You can now treat that $DB object just as you would with ADOdb to create a connection to the mysql database 'testdb'. Note: I have not refactored the error or transaction functions as they were not used in the specific projects I needed this conversion in... but they would be very simple to add.

