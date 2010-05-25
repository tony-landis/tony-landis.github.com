--- 
layout: posts
categories: python
title: FormEncode - Validate Date is in Future
tags: python pylons date formencode
---

FormEncode - Validate Date is on Today or in the Future
=======================================================

Here is a simple validator I wrote for [FormEncode](http://formencode.org/) to check that the input is a valid date, and is >= today.

If you want to use YYYY-MM-DD date formatting instead, just edit the string in line 6 to <code>'%Y-%m-%d'</code>. You will also want to change the error text on line 11.

{% highlight python linenos %}
import datetime, formencode

class DateTodayOrFuture(formencode.FancyValidator):
	def _to_python(self, value, state):
		try:
			val = datetime.date.strptime(str(value), '%m/%d/%Y')
		except ValueError:
			val = None
		if not val:
			raise formencode.Invalid(\
				'This date is invalid, it must be mm/dd/yyyy', value, state)
		else:
			if date(val.year, val.month, val.day) < datetime.date.today():
				raise formencode.Invalid(\
					'The date must be on or after today', value, state)
		return value
{% endhighlight %}

[FormEncode date validators](http://formencode.org/modules/validators.html#dates-and-times)
