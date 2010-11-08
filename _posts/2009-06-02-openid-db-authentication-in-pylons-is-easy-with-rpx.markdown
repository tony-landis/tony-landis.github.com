--- 
layout: posts
title: OpenID + DB Authentication in Pylons is easy with RPX
tags: pylons python openid rpxnow
---

OpenID + DB Authentication in Pylons is easy with RPX
=====================================================

This post will walk you through the steps I took to enable OpenID login in a project I am working on. I wanted to enable OpenID alongside existing user data stored in MySQL database.

<div style="text-align:center;">
<a href="/image/rpx1.png"><img class="size-full wp-image-100" title="pylons-openid-rpx-authentication" src="http://tonylandis.com/wp-content/uploads/2009/06/picture-1.png" alt="Login Screen for Pylons" height="400" /></a>
</div>

This tutorial actually covers more than just the subject of OpenID; it shows how to create your own custom authentication and authorization in Pylons, and how to create your own authorization decorators to protect Pylons actions.

I will assume you already have Pylons, SQLAlchemy, Mako, and MySQL installed, and will start by creating a new Pylons project project for this tutorial.

At this point, I should explain the database schema I have in place for the users who will be able to login with a username/password combination. It is very simple - the USER table stores all the users and their a md5 of their password (for the sake of this tutorial only, you should probably use something more secure) and the USER_OPENID table stores OpenID user information.

When a normal user (with a username/password in the USER table) attempts to login we will check against the USER table.

When a user logs in through OpenID, we will check against the USER_OPENID table to see if they have previously logged in with that OpenID, and if not, we will create both a USER and an USER_OPENID record for them. The end result is that they have a Pylons session which will contain their details from the columns in the USER table.

The 'acl' column in the USER table stores a string that identifies the access level of the user, such as 'admin', 'employee', 'customer', etc. You may want to change this column to an ENUM so the field is restricted to whatever your actual access level identities are. For example: enum('root','staff','customer')

Here are the mysql explain results:

{% highlight ruby %}
$ mysql> explain user;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| username  | varchar(128) | YES  | UNI | NULL    |                |
| password  | varchar(128) | YES  |     | NULL    |                |
| acl       | varchar(16)  | NO   |     | NULL    |                |
| name      | varchar(32)  | YES  |     | NULL    |                |
| dateLogin | datetime     | YES  |     | NULL    |                |
| sessionId | varchar(32)  | YES  |     | NULL    |                |
| ip        | varchar(16)  | YES  |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+

$ mysql> explain user_openid;
+-------------------+--------------+------+-----+---------+----------------+
| Field             | Type         | Null | Key | Default | Extra          |
+-------------------+--------------+------+-----+---------+----------------+
| id                | int(11)      | NO   | PRI | NULL    | auto_increment |
| user_id           | int(11)      | NO   |     | NULL    |                |
| verifiedEmail     | varchar(200) | YES  |     | NULL    |                |
| displayName       | varchar(200) | YES  |     | NULL    |                |
| preferredUsername | varchar(200) | YES  |     | NULL    |                |
| providerName      | varchar(100) | YES  |     | NULL    |                |
| identifier        | varchar(200) | YES  |     | NULL    |                |
| email             | varchar(200) | YES  |     | NULL    |                |
+-------------------+--------------+------+-----+---------+----------------+
{% endhighlight %}

So let's get started by creating a database named 'pylons_openid', and create the tables. If you already have a user table, that you plan to use, that is fine, you will just need to modify the SQLAlchemy model so your columns match up. Here are the MySQL commands to create a database and tables and a few sample users:
 
<script src="http://gist.github.com/122663.js"></script>

One last step of preparation before moving on to Pylons: you will need a <a href="https://rpxnow.com/" target="_blank">free rpxnow account</a>, just head over to their site and sign up. Believe me, using this service takes the headache out of tying into OpenID. You will need to create an "Application" and note the API key and insert it into your development.ini file (explained in a moment)

<div style="text-align:center;">
<a href="/image/rpx2.png"><img class="size-full wp-image-100" title="pylons-openid-rpx-authentication" src="http://tonylandis.com/wp-content/uploads/2009/06/rpx-1.png" alt="Login Screen for Pylons" width="500" /></a>
</div>

Great, now, lets move on to Pylons. Let's create a new pylons project called 'pylons_openid':

<pre class="terminal"><code>$ paster create -t pylons pylons_openid</code></pre>

When prompted, select 'mako' for your template_engine and 'True' for sqlalchemy.

Next, let's create two controllers:

<pre class="terminal"><code>$ cd pylons_openid
$ paster controller auth
$ paster controller root</code></pre>

Next, in the pylons project open model/init.py and define the SQLAlchemy model. Here is the code:

<script src="http://gist.github.com/122669.js"></script>

Next, open development.ini and add the database connection under the [app:main] section - we will also add that RPXnow API key at the same time:

<pre><code># SQLAlchemy database URL
sqlalchemy.url = mysql://root@127.0.0.1:3306/pylons_openid
rpx_token = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx</code></pre>


Let's save all this and test our that our DB connection and SQLAlchemy model are working via paster shell:

<pre class="terminal"><code>$ paster shell development.ini
$ from pylons_openid.model import *
$ print sa.query(User).filter(User.username=='tony').one()
$ 16:21:10,868 INFO  [sqlalchemy.engine.base.Engine.0x...f0f0] ['tony']
$ Tony Landis</code></pre>

Next, in the 'lib' directory, create a file named 'auth.py'. This will contain the decorators needed to protect our actions. Here is the code:

<script src="http://gist.github.com/122670.js"></script>


And now lets setup a controller with some of the actions protected with the @require_ decorators. Open controllers/root.py and change it to this code:

<script src="http://gist.github.com/122671.js"></script>


And now controllers/auth.py. This controller contains the login/logout actions as well as the action that receives the post-back from RPXNow after an OpenID login.

<script src="http://gist.github.com/122674.js"></script>


Now add two templates in the /templates directory (index.html and login.html):

<script src="http://gist.github.com/122675.js"></script>
<script src="http://gist.github.com/122676.js"></script>


We can now start up this server:

<pre class="terminal"><code>$ paster serve --reload development.ini</code></pre>


Now load <a href="http://127.0.0.1:5000/root/index" target="_blank">http://127.0.0.1:5000/root/index</a> in your browser and you should be redirected to the login screen, since we have the @require_login decorator on that action. Screenshot below:


<div style="text-align:center;">
<a href="/image/rpx3.png"><img class="size-full wp-image-100" title="pylons-openid-rpx-authentication" src="http://tonylandis.com/wp-content/uploads/2009/06/picture-1.png" alt="Login Screen for Pylons" width="500" height="567" /></a>
</div>


Thats all there is to it! At this point you should be able login with a username/password of tony/password and customer/password, as well as login using the RPXnow powered OpenID option. After logging in through one of the OpenID providers, you can check the USER and USER_OPENID tables to see that the new records have been inserted as expected with the 'Customer' ACL.

If you want to add new ACLs or groups of ACLs, just edit the lib/auth.py file, it is very simple. 
If you create more decorators and plan to do <code>from project.lib.auth import * </code>
as shown in the example root controller, then remember to add the new decorator names to the __all__
list at the top of lib/auth.py.

No doubt my ACL approach is overly simplistic for some projects, and perhaps overly complicated for others, but as the documentation of authentication and authorization in the realm of Pylons fell a bit lower than my expectations, I hope these examples will save others traveling a similar path a bit of headache.

And thanks to RPXNow for hands down one of what has to be the biggest timesaver for developers tackling OpenID!
