--- 
layout: posts
categories: php
title: Smarty for High Volume Database Driven Applications
tags: smarty php performance
---

Smarty for High Volume Database Driven Applications
===================================================

This is not meant to bash <a href="http://smarty.php.net/rightforme.php">Smarty</a> in any way - I think it is a great library and the developers behind it obviously did an excellent job. That being said, it is not the right fit for every job. I have personally stopped using Smarty in database driven applications I develop for the following reasons.

<strong>1. Reduced Performance</strong>

Performance is my single biggest issue. Most of my applications are database driven and 99% of the pages must be completely refreshed on each view. So using Smarty's caching features is out of the picture. This means I am including a huge library to generate and include Smarty generated PHP code, rather than a hand coded version that I could optimize and tweak. All template engines create additional includes and processing and Smarty is no exception.

<strong>2. Large Footprint</strong>

As of this writing, the Smarty library consists of 71 files consuming nearly 1/2 MB on disk. I am building a framework to be used by a number of applications, most of which are rather small. I wish to avoid this huge footprint, as I want to be able to drop my framework onto a webserver via FTP or a small archive in a minute, not an hour. This huge footprint ties back into my performance issues as well. More files = more includes = increased IO requirements.

<strong>3. Separates me from my Programming Language of Choice</strong>

I chose to develop my framework and applications in PHP, it is my language of choice and I am most comfortable working in it. I don't want to have it's power restricted by the limited featureset of a separate technology, or being forced to write a plugin for Smarty in order to do something natively available in PHP.

<strong>4. Relearning is a Waste of my Time</strong>

I cannot count the times I wanted to do something that would be so simple in PHP but was either unsupported by Smarty or required some methodology that felt completely hacky. It is a waste of time to learn a second method of coding something unless it is an improved method, and I don't feel that letting Smarty generate my PHP code for me is an improved method. If it were then all the more reason I should be coding in PHP more! The same goes for anyone I hire to do further development on a project.

<strong>5. Doesn't Separate Logic from Design</strong>

Being able to use <span id="code">{$var}</span> vs. <span id="code">&lt;?php echo $var ?&gt;</span> (tag replacement) is convenient, but does not justify the large footprint for me. Using Smarty for anything other than simple tag replacement (ie: if,else,foreach,assign) means the logic is NOT separated from the design.

<strong>6. One more Dependency</strong>

Using calls to native PHP functions is dependency-free. For me, Smarty amounts to a huge dependency that must be maintained and included, which can be time consuming when dealing with many instances of an application.

<strong>7. Extra Configuration Required</strong>

Smarty requires a compile directory - this is one more thing to have to create, CHMOD, etc... Once again, when installing multiple instances of an application this increases the time required.

<strong>Conclusion</strong>

Smarty still has it's place - I use it for templating purposes on several websites where full caching can be performed. It would still be faster to generate the static HTML and then serve it without ever calling into PHP, which is a topic I will address in the future.

So for me, Smarty will not ever be used in a high-volume database driven application. It still has a place for building cached webpages though. I build backend services that can scale to high volumes of requests, and on the websites where that data is displayed, use Smarty to cache the webpage containing the data from the service.
