--- 
layout: posts
categories: php
title: JSON Plugin for Smarty
tags: php smarty json 
---

JSON Plugin for Smarty
======================

This is a simple plugin for Smarty to retrieve and parse JSON data into an object in Smarty.
Requires the PECL JSON.php class or PHP 5.2.0.

Instructions
------------
<ol>
	<li>If your PHP version is &lt; 5.2.0 <a href="http://pecl.php.net/package/json" target="_blank">Download the required JSON.php class</a> and save it to your include path if it is not already there. If your PHP version is &gt;= 5.2.0, this class is not needed, we will use the native <a href="http://us.php.net/json_decode" target="_blank">json_decode()</a> function.</li>
	<li>Download the JSON Smarty Plugin PHP Function File> (shown in the gist below) as function.json.php in your smarty\plugins\ directory.</li>
</ol>

Example plugin usage in Smarty templates files:

<pre><code>{json url=http://site.com/data.json resource=result}</code></pre>

The <code>$result</code> variable now contains the parsed JSON data.

<script src="http://gist.github.com/31451.js"></script>
