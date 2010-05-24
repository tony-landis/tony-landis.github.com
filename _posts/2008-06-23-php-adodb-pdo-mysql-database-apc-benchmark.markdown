--- 
layout: posts
categories: performance
title: Benchmarking PDO and ADOdb Database Abstraction Libraries
tags: php benchmark performance pdo adodb
---

Benchmarking PDO and ADOdb Database Abstraction Libraries
=========================================================

I was unable to locate a satisfactory <a href="../../articles/accurate-web-application-benchmarking-methodology.htm">benchmark</a> of PDO vs. ADOdb, so I decided create some to get an idea of the performance differences.

I expected PDO to beat ADOdb easily, since PDO is a native PHP library and requires no run-time include. See my <a href="../../articles/accurate-web-application-benchmarking-methodology.htm">Web App benchmarking methodology</a> here to understand why I was unsatisfied with the existing PDO vs ADOdb and PEAR::NET library benchmarks I found online.

<h3>PDO vs ADOdb Benchmark - Select and Loop</h3>
<script src="http://gist.github.com/31483.js"></script>

<div id="tbl" class="zebra borders">
<br>
<table width="100%" >
<caption>
<strong>MySQL SELECT Benchmark Results, 1000 Requests, APC Disabled</strong></caption>
<thead>
<tr>
<td>Library</td>
<td>Concurrency</td>
<td>Total Time</td>
<td>Requests/Sec.</td>
<td>Speedup/Slowdown</td>
</tr>
</thead>
<tbody>
<tr>
<td>ADOdb</td>
<td>1</td>
<td align="right">20.90/sec</td>
<td align="right">47.84</td>
<td align="right">-</td>
</tr>
<tr>
<td>PDO</td>
<td>1</td>
<td align="right">0.73/sec</td>
<td align="right">1358.62</td>
<td align="right"><span class="style1">+2840%</span></td>
</tr>
<tr>
<td>ADOdb</td>
<td>50</td>
<td align="right">10.78/sec</td>
<td align="right">99.23</td>
<td align="right">-</td>
</tr>
<tr>
<td>PDO</td>
<td>50</td>
<td align="right">0.54/sec</td>
<td align="right">1850.90</td>
<td align="right"><span class="style1">+1865%</span></td>
</tr>
<tr>
<td>ADOdb</td>
<td>100</td>
<td align="right">10.44/sec</td>
<td align="right">95.78</td>
<td align="right">-</td>
</tr>
<tr>
<td>PDO</td>
<td>100</td>
<td align="right">0.53/sec</td>
<td align="right">1869.33</td>
<td align="right"><span class="style1">+1952%</span></td>
</tr>
</tbody></table>


APC should give ADOdb an advantage, here are the results of the same benchmark with APC enabled. The performance times for the PDO benchmarks with APC enabled did not change significantly as there were only 3 lines of code for APC to cache.

<br>
<br>
<table>
<caption><strong>MySQL SELECT Benchmark Results, 1000 Requests, APC Enabled</strong></caption>
<thead>
<tr>
<td width="55">Library</td>
<td width="98">Concurrency</td>
<td width="86">Total Time</td>
<td width="109">Requests/Sec.</td>
<td width="203">APC Speedup</td>
</tr>
</tbody>
<tbody>
<tr>
<td>ADOdb</td>
<td>1</td>
<td align="right">1.65/sec</td>
<td align="right">604.52</td>
<td align="right">+1264% (vs no APC)</td>
</tr>
<tr>
<td>PDO</td>
<td>1</td>
<td align="right">-</td>
<td align="right">-</td>
<td align="right">+225% (vs ADOdb)</td>
</tr>
<tr>
<td>ADOdb</td>
<td>50</td>
<td align="right">1.25/sec</td>
<td align="right">798.26</td>
<td align="right">+795% (vs no APC)</td>
</tr>
<tr>
<td>PDO</td>
<td>50</td>
<td align="right">-</td>
<td align="right">-</td>
<td align="right">+232% (vs ADOdb)</td>
</tr>
<tr>
<td>ADOdb</td>
<td>100</td>
<td align="right">1.33/sec</td>
<td align="right">751.72</td>
<td align="right">+785% (vs no APC)</td>
</tr>
<tr>
<td>PDO</td>
<td>100</td>
<td align="right">-</td>
<td align="right">-</td>
<td align="right">+249% (vs ADOdb)</td>
</tr>
</tbody></table>
</div>


<h2>Conclusion</h2>
If performance is critical, don't consider ADOdb unless an optimizer such as APC is installed. Even then, I do not recommend ADOdb unless ADOdb offers some critical feature that is missing in PDO. Even with APC installed the best run of ADOdb was 225% slower than with PDO.

Why? PDO is a native compiled library and not loaded at runtime.

What about PEAR::NET? I have never used this library so I didn't go to the bother of including it in my benchmark. Also, the purpose of this benchmark was to demonstrate the speedup of using the native PHP library vs a large included library, and I feel that the PEAR library will perform in a similar fashion as the ADOdb library if it is anywhere near the same size and complexity. That is an assumption however...

<hr>
<h2>Addendum</h2>
<em>6/5/2008</em>

I was questioned on my choice to exclude ADOdb with the ADOdb extension from this benchmark. I had given it little though at the start of this benchmark and decided to run the benchmark again to compare ADOdb with the extension installed to PDO's best performance. I ran each benchmark here with and without APC enabled.

<div id="tbl" class="zebra borders">
<table>
<caption><strong>MySQL SELECT Benchmark Results, 1000 Requests, ADOdb Extension</strong></caption>
<thead>
<tr>
<td width="55">APC</td>
<td width="98">Concurrency</td>
<td width="86">Total Time</td>
<td width="109">Requests/Sec.</td>
<td width="203">Slowdown vs. PDO</td>
</tr>
</thead>
<tbody>
<tr>
<td>No</td>
<td>1</td>
<td align="right">18.38/sec</td>
<td align="right">54.39</td>
<td align="right">2498%</td>
</tr>
<tr>
<td>Yes</td>
<td>1</td>
<td align="right">1.63/sec</td>
<td align="right">613.87</td>
<td align="right">221%</td>
</tr>
<tr>
<td>No</td>
<td>50</td>
<td align="right">9.98/sec</td>
<td align="right">100.18</td>
<td align="right">1848%</td>
</tr>
<tr>
<td>Yes</td>
<td>50</td>
<td align="right">1.13/sec</td>
<td align="right">878.63</td>
<td align="right">211%</td>
</tr>
<tr>
<td>No</td>
<td>100</td>
<td align="right">9.75/sec</td>
<td align="right">102.55</td>
<td align="right">1823%</td>
</tr>
<tr>
<td>Yes</td>
<td>100</td>
<td align="right">1.2/sec</td>
<td align="right">832.71</td>
<td align="right">224%</td>
</tr>
</tbody></table>

My conclusion regarding the ADOdb extension: Using my benchmarking methodology, there is little gain to be had with the extension enabled vs without it. I feel this is due to the fact that the ADOdb library must still be included at runtime even with the extension enabled in php.ini.

In my environment, to get within almost half of the speed of PDO, I need two PHP extensions enabled - APC and the ADOdb C extension. PDO remains my recommendation for a database abstraction library from a performance standpoint.

Another addition regarding my test environment: Intel Duo 2Ghz, 2GB RAM, Ubuntu 7.10, PHP 5.2.3 CGI, lighttpd 1.4.18. The disk is slow (5400rpm) so I suspect the slower IO is is increasing the library inclusion load time.
