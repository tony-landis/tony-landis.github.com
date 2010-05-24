---
layout: posts
category: python
title: Installing psycopg2 on OSX 10.5
tags: python pylons postegresql
- 
---

Installing psycopg2 on OSX 10.5
===============================

Just a little tip if you get stuck installing the PostgreSQL Python database adaptor as I did. Here is the error I got when running setup.py install (I downloaded the source after easy_install failed to work):

<pre class="terminal"><code>building 'psycopg2._psycopg' extension
Compiling with an SDK that doesn't seem to exist: 
	/Developer/SDKs/MacOSX10.4u.sdk
Please check your Xcode installation</code></pre>

The solution is to download the psycopg2 source, then edit the setup.py - 
around line 426 before after <code>undef_macros[]</code> add this:

{% highlight python %}
,extra_link_args = ['-L/Developer/SDKs/MacOSX10.5.sdk/usr/lib']
{% endhighlight %}

So that line will look like this:

{% highlight python %}
undef_macros=[], extra_link_args=['-L/Developer/SDKs/MacOSX10.5.sdk/usr/lib']))
{% endhighlight %}

That is all, running <pre class="terminal"><code>$ sudo python setup.py install</code></pre> should get it working.
