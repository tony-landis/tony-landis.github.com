--- 
layout: posts
categories: benchmark 
title: Accurate Web Application Benchmarking Methodology
tags: benchmark methodology 
---

Accurate Web Application Benchmarking Methodology
=================================================

I recently was searching for a benchmark comparing the <a href="/benchmark/an-accurate-web-application-benchmarking-methodology/">performance of the PDO and ADOdb Database Abstraction Libraries for PHP</a> applicable to use in a Web application, and came up with nothing satisfactory on the subject. There were several benchmarks floating around but I noticed a problem with the methodology used.
<h2>A Flawed Methodology</h2>
<ol>
	<li>Create a separate script for each library to be benchmarked</li>
	<li>Within that script, create a time marker at the start and end of the script</li>
	<li>After the first time marker, include the library</li>
	<li>Between the time markers, execute X (example: X=500) iterations of a block of code which calls into the method(s) of the library</li>
</ol>
The benchmarkers then executed each script and calculated the time difference between the start/end time markers of each script to determine the winning library.
<h2>Why this Benchmarking Methodology is less Accurate</h2>
When benchmarking libraries to be included at runtime in a PHP driven web application, there will be varying overhead for the actual inclusion of the library. I would assume this applies not only to PHP, but to languages such as Python, Perl, Ruby, etc.

Thus any benchmarking methodology which fails to factor in the library inclusion cost in a realistic proportion to the calls to that library will be skewed, sometimes badly. Never in my experience have I needed to expose performance-critical code that iterates through 500 calls to a database abstraction library per a single hit. This would be a ration of 1 library inclusion per 500 method calls into that library.

A more accurate ratio of library includes to library method calls is 1 to 3. So on an average, for one hit to an application where the library is included, we call methods of that library three times.
<h2>A More Accurate Benchmarking Methodology</h2>
We are benchmarking a web application, so we need our 500 iterations on the client side, not inside a high count loop inside the application.

Each of our client side iterations will be a separate request causing our library to be loaded once, and methods of that library to be called in a realistic ratio that would simulate real application calls.

To achieve this, we use a tool such as ApacheBench on the client side and make 500 requests (example below). We still have a script for each library we wish to benchmark, but we model the method calls within that script to a more realistic number, such as three.
;w;
<pre class="terminal"><code># Library A results
ab -n 500 http://localhost/library-a.php

# Library B results
ab -n 500 http://localhost/library-b.php</code></pre>

<br>
<h2>The Result</h2>
In the case of <a href="/benchmark/an-accurate-web-application-benchmarking-methodology/">Benchmarking PDO vs ADOdb</a>, I saw benchmarks using the flawed benchmark methodology which put   PDO at only a 125% speedup over the ADOdb library.

When I benchmarked (<a href="/benchmark/an-accurate-web-application-benchmarking-methodology/">see full benchmark here</a>) the PDO library provide as much as a <span class="style1">2840%</span> speedup over  the ADOdb Library.

My conclusion - load time inclusion times in web languages makes a huge difference.
