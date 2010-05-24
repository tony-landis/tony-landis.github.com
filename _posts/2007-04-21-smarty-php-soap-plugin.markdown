--- 
layout: posts
categories: php
title: SOAP Plugin for Smarty
tags: soap php smarty plugin
---

SOAP Plugin for Smarty
======================

This plugin for Smarty enables in-template access to SOAP services.

It requires the NuSOAP PHP class in your PHP include folder (ie: includes_path/nusoap/nusoap.php) or edit the path in the soap smarty plugin.

You may need to download the NuSOAP php class and place it in your PHP include path.



Example SOAP plugin usage in Smarty templates:

<pre><code>{soap assign=soapResponse
 endpoint=http://site.com/resource.soap
 call=resource..search
 var=(string)Test
 debug=true|false}</code></pre>

The smarty soap plugin function is shown below.

<script src="http://gist.github.com/31456.js"></script>
