--- 
layout: posts
categories: jquery
title: jQuery Plugin - YUI Calendar Selector
tags: jquery plugins yui date
---

jQuery Plugin - YUI Calendar Selector
=====================================

I love using [jQuery](http://www.jquery.org) and do so at every chance possible. However, I have yet to find a calendar plugin that fits my needs as well as the [YUI Calendar](http://developer.yahoo.com/yui/calendar/).

Frequently, I need to attach the calendar to form fields for date selection, and I didn't like introducing all the YUI CSS and JS includes into the code. Since I like the jQuery way of doing things, I decided to write a little plugin so binding the calendar popup to a form field would be this simple:

Example 1: <input id="date1" type="text" size="12" autocomplete="off" /> mm/dd/yyyy

Example 2: <input id="date2" type="text" size="12" autocomplete="off" /> yyyy-mm-dd

{% highlight javascript %}
<script type="text/javascript" src="/js/jquery.selectdate.js"></script>
<script type="text/javascript" charset="utf-8">
$(document).ready(function() {
  $('#date1').selectdate({format:'mm/dd/yyyy', pages:3, font_size:'85%'});
  $('#date2').selectdate({format:'mm-dd-yyy', pages:2});
});
</script>
{% endhighlight %}

You will notice the following are easily configurable:

*  The date format, either yyyy-mm-dd or mm/dd/yyyy
*  The number of calendar pages to display
*  The font size, this is handy when you want to show a large number of pages in a small area

Source Code for the plugin

[http://github.com/tony-landis/jquery-plugins/blob/master/selectdate/jquery.selectdate.js](http://github.com/tony-landis/jquery-plugins/blob/master/selectdate/jquery.selectdate.js)

<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.2.6/jquery.min.js"></script> 
<script type="text/javascript" src="http://github.com/tony-landis/jquery-plugins/raw/master/selectdate/jquery.selectdate.js"></script>
<script type="text/javascript" charset="utf-8">
$(document).ready(function() {
  $('#date1').selectdate({format:'mm/dd/yyyy', pages:3, font_size:'85%'});
  $('#date2').selectdate({format:'yyyy-mm-dd', pages:2});
});
</script>

<!--
Binding Multiple Date Inputs
----------------------------

One other case I run into a lot is the need to select a date range, start and end. In many cases, it would be usefull for the user if the input selector behaved so that these two dates were linked. So I also added a feature in the plugin so that changing the @start@ date will change to the @end@ date, always keeping the number of days between the two fields in sync.

If the user changes the @end@ date, then the number of days the plugin will maintain betwee the dates is changed. So changing the @end@ date does not modify the @start@ date, it only changes the rule for the plugin.

Start Date: <input id="start_date" class="cal-bind-1" type="text" size="12" autocomplete="off" />
End Date: <input id="end_date" class="cal-bind-2" type="text" size="12" autocomplete="off" />

  $('#start_date').selectdate({format:'mm/dd/yyyy', pages:2, bind:'1'});
  $('#end_date').selectdate({format:'mm/dd/yyyy', pages:2, bind:'2'});

-->

