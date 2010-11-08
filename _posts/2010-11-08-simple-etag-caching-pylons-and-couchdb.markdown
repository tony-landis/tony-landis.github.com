---
layout: posts
categories: pylons couchdb etag cache
title: A Caching Strategy for my WebApp - Couchdb and ETags to the rescue
---

A Caching Strategy for my WebApp - Couchdb and ETags to the rescue
===

I use [Pylons](http://pylonshq.com) to serve up a website with several 
thousand pages. Until recently, all the website content was stored in MySQL and I 
used [SQLAlchemy](http://www.sqlalchemy.org) as my ORM. Caching was done with the Pylons 
[beaker_cache](http://beaker.groovie.org/modules/cache.html) 
decorator together with [memcached](http://memcached.org/).

While beaker_cache and memcached certainly met the simple objective of caching the 
first request to any given webpage and allowing subsequent request to the cached
page to be served < 300ms, there is a major trade off. Pylons provides no way to 
invalidate a single memcached object by key. That means if a record in MySQL gets
edited, there is no way to either automatically or manually clear the cached
webpage generated from that record.

So my choices have been either invalidate everything, 
or invalidate nothing and wait until the 
individual memcached key expires for the page to regenerate.


Cache invalidation should not be performed on a method level
==

Even if there was a working method-level cache invalidation capability within
the Pylons framework, I still would be unsatisfied. To invalidate
the caches of all webpages affected by an update to a DB record, I must know all
the controllers and methods where that record is being cached, as that is how
the key for the memcached object is generated. 

Code maintenance for the method level approach will become a hellish nightmare, 
as suddenly I have to track all these relations between DB records and where
methods where they are accessed and cached. 


The alternative: Cache based on record revisions
==

In order to do this efficiently, our data store needs to keep track of the revisions 
made to a record. MySQL does not have this built in. 

We could add a hash field on every one of these MySQL tables
that will act as a ETag and get updated every time a record is updated.

However, that would put us back into the realm of code maintenance - we now
have to make sure that on each MySQL UPDATE we set a new revision
hash. An overcomplication introducing code maintenance issues.


ETags and Couchdb to the rescue
==

In order to meet some other objectives outside the scope of this post, I 
recently migrated the content for this app from MySQL to 
[Couchdb](http://couchdb.apache.org).

Fortunately, Couchdb automatically maintains the revision on each document,
and those revisions will work perfectly as 
[ETags](http://en.wikipedia.org/wiki/HTTP_ETag).
I hacked this caching solution together for Pylons.

{% highlight python %}

"""

The etag_check() decorator checks if an ETag was received from the client
and is in our list of valid ETags. If so, it skips the method 
entirely and return a 304 HTTP Not Modified Response. 

If there is not a match in the ETags list, the requested method
is called.

Inside the requested method, etag_set() is used to send the ETag
to the client, and add the ETag to the ETags list for future lookup.
The couchdb doc._id is added to the Keys dictionary with the value 
of doc._rev. This is so we can quickly invalidate the ETag associated
with a given couchdb document.

To remove a specific ETag, pass it to etag_remove() or 
remove any ETag for a specific Couchdb document by passing the doc._id
to etag_remove_by_key()

"""

import pylons
from decorator import decorator
from webob.exc import status_map
import re

IF_NONE_MATCH = re.compile('(?:W/)?(?:"([^"]*)",?\s*)')

ETags = []
Keys  = {}

def etag_remove(etags):
	"removes an etag to force refresh"
	for etag in etags:
		if etag in ETags:
			ETags.remove(etag)
			#print "Removed ETag %s" % etag

def etag_remove_by_key(key):
	"removes etag for the given key"
	if key in Keys:
		etag = Keys[key]
		etag_remove([etag])

def etag_exists(etags):
	"looks through etags for a match"
	for etag in etags:
		if etag in ETags:
			return True
	return False

def etag_cache(_rev, _id):
	"send/store the etag for future checking"
	response = pylons.response._current_obj()
	response.headers['ETag'] = '"%s"' % _rev
	if _rev not in ETags:
		ETags.append(_rev)
	if _id:
		Keys[_id] = _rev
	#print "ADDED ETAG: %s, %s" % (_rev, _id)

@decorator
def etag_check(func, *args, **kwargs):
	"check if the client sent an etag that is in our list "
	etags = IF_NONE_MATCH.findall(
		pylons.request.environ.get('HTTP_IF_NONE_MATCH', ''))
	if etag_exists(etags):
		#print "ETag match, returning 304 HTTP Not Modified Response"
		pylons.response.headers.pop('Content-Type', None)
		pylons.response.headers.pop('Cache-Control', None)
		pylons.response.headers.pop('Pragma', None)
		raise status_map[304]().exception
	else:
		#print "ETag didn't match, returning response object"
		return func(*args, **kwargs)

{% endhighlight %}

This file is saved to myapp/lib/etag_cache.py. Example usage in an Pylons controller
using [Couchdbkit](http://couchdbkit.org/) to interface with couchdb follows. 
The index() method sets/checks the ETags and the save() method clears the ETag for document.

{% highlight python %}
from myapp.lib.etag_cache import etag_cache, etag_set, etag_remove_by_key

class SomethingController(BaseController):

	@etag_check
	def index(self, id):
		"public cached view"
		c.doc = Something.get(id)
		# send the ETag to the client, 
		# and add it to our list of valid ETags
		etag_cache(c.doc._rev, c.doc._id)
		return str(c.doc)
	
	def save(self, id):
		"save changes to the doc"
		doc = Something.get(id)
		doc.save()
		# Invalidate the previous ETag stored for this couchdb doc
		etag_remove_by_key(c.doc._id)
		redirect(url(controller="something", action="edit"))

{% endhighlight %}

That is it. Of course without some proxy / cache like
[Varnish](http://www.varnish-cache.org/)
between the client and the app, we are not going to see much of a load. 
Most webservers such as
[Lighttpd](http://www.lighttpd.net) and
[Nginx](http://www.nginx.org) 
can be setup as a 
[Reverse Proxy](http://en.wikipedia.org/wiki/Reverse_proxy) 
with caching pretty easily.

Another interesting undertaking would be to 
[listen to the couchdb changes](http://couchdbkit.org/docs/changes_consumer.html)
and pass any changed doc._id to the etag_remove_by_key() method. This would
mean that etag_remove_by_key() would not need to be added anywhere
else in the app to invalidate ETags, since you can use the couchdb asynchronous
option and continually be updated with any changes.

Thanks for wading through my brain dump, any comments are welcome.
