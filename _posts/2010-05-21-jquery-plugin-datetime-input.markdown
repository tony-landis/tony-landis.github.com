--- 
layout: posts
category: code
title: jQuery Plugin - Date/Time Input
tags: jquery plugin date 
---

jQuery Plugin - Date/Time Input
===============================

I needed a simple, intuitive date time selector and couldn't find an exact match for what I was wanting to do.

So I wrote this simple jQuery plugin to take a normal input field containing 24-hour formatted time (HH:MM:SS), and create options for the selection of hour, minute, AM/PM, updating the user's selection back to 24 hour format in the input field, which the plugin will hide by default.

<a href="http://tony-landis.github.com/jquery-plugins/">
<img class="indent" title="Screenshot of time selection" src="http://tony-landis.github.com/jquery-plugins/example.png" alt="Screenshot, links are below" width="460" height="149" /></a>

One thing I noticed while coding this - the 12-hour clock system is very non-intuitive, at least compared to the 24-hour system which just goes 00:00:00 - 23:59:59. <a title="24/12 hour clock confusion" href="http://en.wikipedia.org/wiki/12-hour_clock#Confusion_at_noon_and_midnight">Read this to see what I mean.</a>

At any rate, the plugin works for me.

<a title="jQuery Select Time plugin" href="http://tony-landis.github.com/jquery-plugins/">Example usage of the select time plugin.</a>

<a title="jQuery select time (hour/minute) code" href="http://github.com/tony-landis/jquery-plugins/tree/master/selecttime/">Github repository with the select time code.</a>

Enjoy, and let me know of any bugs or improvements.
