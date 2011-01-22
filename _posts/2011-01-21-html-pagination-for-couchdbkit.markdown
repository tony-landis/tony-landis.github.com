---
layout: posts
categories: pylons couchdb couchdbkit python
title: Pagination Class for Couchdbkit Views
---

Pagination Class for Couchdbkit Views
===


When using [Pylons](http://pylonshq.com) and have found the
[WebHelpers](http://pypi.python.org/pypi/WebHelpers) package to be very usefull.

I have been using CouchDB and [Couchdbkit](http://couchdbkit.org/) and do miss
the ability to use the WebHelpers pagination that I grew accustomed to using
with SQLAlchemy queries, so I hacked this little class.

This library should work in any web application framework, not just Pylons.
Just pass in your params when you call Pager(), in pylons it would be
`Pager(view, params=request.params)`

The concept is simple - just pass a Couchdbkit view into this class and you
have an object that contains the origonal couchdbkit view plus a few helper
functions for generating a textual status of what page you are currently on,
as well as the HTML needed to page through the results.

{% highlight python %}

"""
HTML Pagination Generation Library for Couchdbkit

Example Usage:

	>>> # Get a couchdbkit ViewResults object
	>>> view = Greeting.view('greeting/all')

	>>> # Pass the view into the pager, giving it access to the
	>>> # the params dictionary so the pagination links are 
	>>> # populated with any args from the request: &key=val 
	>>> pager = Pager(view, params={key=val, foo=bar}, limit=20)

	>>> # Show the results status
	>>> pager.status()
	>>> "Page 1 of 10, 200 results found"

	>>> # Show the paging links, will display the first, last, and 10
	>>> # closest pages surrounding the page requested
	>>> pager.pager(10)

	>>> # Access the couchdbkit view:
	>>> pager.view.all()

"""

import math

class Pager(object):
	view = None
	params = None
	page_limit_arg = None
	page_no_arg = None
	result_count = 0
	page_count = 0
	page_limit = 0
	page_no = 1
	def __init__(self, view, params=None, 
		page_limit_arg='page_limit', page_no_arg='page_no', 
		limit=20, **kwargs):
		self.view = view
		self.params = params
		self.page_limit_arg = page_limit_arg
		self.page_no_arg = page_no_arg
		# get the skip / limit values
		self.page_limit = int(params.get(page_limit_arg, limit))
		self.page_no = int(params.get(page_no_arg, 1))
		# set the skip / limit for couchdb
		self.view.params.update(
			dict(
				limit=self.page_limit, 
				skip=(self.page_no-1) * self.page_limit))
		# count results
		self.result_count = view.total_rows
		#self.result_count -= view.offset
		if self.result_count:
			if self.result_count <= self.page_limit:
				self.page_count = 1
			else:
				self.page_count = \
				int(math.ceil(float(self.result_count) / float(self.page_limit))) 
	def all(self):
		return self.view.all()
	def status(self, change=False):
		"show the paging status"
		return 'Page %s of %s, %s results found' % (
			(self.page_no), self.page_count, self.result_count)
	def pager(self, count):
		"show the pager links"
		if not self.page_count > 1: return []
		pages = [1]
		i = self.page_no
		cn = int(math.ceil(count / 2))
		for i in range(cn):
			ii = self.page_no - cn + i
			if ii > 1: pages.append(ii)
		for i in range(cn+1):
			ii = self.page_no + i
			if 1 < ii > 0: pages.append(ii)
		if self.page_count-1 not in pages:
			pages.append(self.page_count)
		href = '?page_no=_PG_'
		for k,v in self.params.iteritems():
			if not k == 'page_no':
				href += '&%s=%s' % (k,v)
		ln = ''
		last = None
		for i in pages:
			if last and (last+1) < i:
				ln += ' ... '
			if i != self.page_no:
				ln += '<a class="pager_link" href="%s">%s</a>' \
					% (href.replace('_PG_', str(i)), i)
			else:
				ln += '<span class="pager_curpage">%i</span>' % i
			last = i
		return ln

{% endhighlight %}

Happy Pagination!
