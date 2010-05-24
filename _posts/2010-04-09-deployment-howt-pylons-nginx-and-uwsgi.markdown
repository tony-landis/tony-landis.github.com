--- 
layout: posts
category: python
title: Deployment Notes for Pylons, Nginx, and uWSGI
tags: python pylons
---

Deployment Notes for Pylons, Nginx, and uWSGI
=============================================

I recently decided to switch from lighttpd to nginx. 
I have one particularly critical Pylons app currently deployed with paster, and after reading <a title="WSGI Benchmark - paster, uwsgi" href="http://nichol.as/benchmark-of-python-web-servers" target="_blank">this Benchmark of Python WSGI servers</a> I decided on <a title="uWSGI is a fast (pure C), self-healing, developer-friendly WSGI server" href="http://projects.unbit.it/uwsgi/" target="_blank">uWSGI</a> for this application.

Getting nginx setup was no problem - just be sure to configure with the uWSGI module:
<pre class="terminal"><code>$ ./configure --add-module=../uwsgi/nginx/</code></pre>
Then follow the normal <a href="http://projects.unbit.it/uwsgi/wiki/Install">uWSGI installation instructions here</a>.

Things got a bit murky for me after this point, as there seems to be a lack of step-by-step information for getting a a Pylons app actually served up using uWSGI+Nginx. Here are the exact steps to follow that finally worked for me.

<strong>1. Install virtualenv and create a virtualevn for your Pylons app:</strong>

<pre class="terminal"><code>$ easy_install virtualenv
$ mkdir ~/virtualenv
$ cd ~/virtualevn
$ virtualenv --distribute myapp
</code></pre>

<strong>2. Install any python libraries needed by your Pylons app:</strong>

<pre class="terminal"><code>$ cd ~/virtualenv/myapp/bin
$ ./easy_install Pylons, SQLAlchemy, MySQL-python, \
   PasteDeploy, Mako, formencode, webhelpers, python-memcached
</code></pre>

<strong>3. Generate an .egg for your Pylons app - change to the root directory of your Pylons app and run these commands:</strong>

<pre class="terminal"><code>$ rm -rf *.egg-info (where * is the name of your pylons app)
$ rm -rf dist
$ rm -rf buid
</code></pre>

Now open the MANIFEST.in file, and edit it so that all these directories will be recursively include: i18n, model, controllers, forms, config, lib, public. Then run the following:

<pre class="terminal"><code>$ python setup.py egg_info
$ python setup.py bdist_egg
</code></pre>

You should now be able to cd to dist/ and see the .egg file. Make a note of the path as you will need it next.

<strong>4. Install the Pylons app .egg in your virtualenv:</strong>

<pre class="terminal"><code>$ cd ~/virtualenv/myapp/bin
$ ./easy_install /path_to_the_egg/the.egg
</code></pre>

You should now be able to cd to <code>~/virtualenv/myapp/lib/pythonX.X/site-packages/</code> and see your egg as well as all the other libraries you installed in step 2.

<strong>5. Setup the server in your Nginx config file:</strong>
<pre><code>server {
  listen			80;
  server_name		www.yoursite.com yoursite.com;
  charset			utf-8;
  root				/path_to_your_pylons_app/public;
  index				index.html index.htm;
  location ~* /(img|js|iepng|css)/ {
    root			/path_to_your_pylons_app/public;
    expires max;
    add_header Cache-Control "public";
    break;
  }
  location / {
    uwsgi_pass	0.0.0.0:3031;
    include			uwsgi_params;
    uwsgi_param	SCRIPT_NAME /;
  }
}</code></pre>

<strong>6. Create your pylons production.ini file and start uWSGI with the path to your virtualenv as the python home dir:</strong>

<pre class="terminal"><code>$ uwsgi --paste config:/production.ini --socket :3031 -H ~/virtualenv/myapp</code></pre>

That is all, I hope it saves you some headache.
