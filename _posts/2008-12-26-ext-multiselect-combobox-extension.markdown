--- 
layout: posts
categories: code
title: MultiSelect ComboBox Component for Ext JS
tags: ext plugin 
---

MultiSelect ComboBox Component for Ext JS
=========================================


Unable to find a ComboBox component for <a title="ExtJS" href="http://extjs.com/" target="_blank">Ext JS</a> that would work in an Ext Grid, permit multiple selections, and support both local and remote datasources with multiple fields (one for display, one for the index), I set out to build my own some time back.

<strong>Note:</strong> Since I began working on this some time back, a similar Ext plugin was developed by another developer: see <a href="http://lovcombo.extjs.eu/">http://lovcombo.extjs.eu/</a>


<link rel="stylesheet" type="text/css" href="http://www.extjs.com/deploy/dev/resources/css/ext-all.css" />
<script type="text/javascript" src="http://www.extjs.com/deploy/dev/adapter/ext/ext-base.js"></script> 
<script type="text/javascript" src="http://www.extjs.com/deploy/dev/ext-all.js"></script>
<script type="text/javascript" src="http://gist.github.com/raw/40145/5e8c9429e00aeb8151db736b8400f01f21644bae/gistfile1.js"></script>
<script type="text/javascript" src="http://gist.github.com/raw/411622/169dcf58800b63d7389723eb267328ccf91b5bb3/gistfile1.js"></script>

<script language="Javascript">
var menu1=null;
Ext.onReady(function(){
	menu1 = new Ext.example.StateMultiSelect({
		renderTo: 'menu',
		containerHeight: 200,
		containerWidth: 200
	}); 	
	grid.render('grid');
}); 
</script>
 
<p>Here is an example of the MultiSelect ComboBox.</p>
<p>
<table>
	<tr>
		<th><div id="menu" style="text-align:left;"></div><th>
		<td><input type="button" value="Show Selected" onclick="alert('Currently Selected States: ' + menu1.getValue() );"></td>
	</tr>
</table>
</p>
 
<p>Here is an example of the MultiSelect menu working in an Ext JS Grid.</p>
<p style="padding-left:10px;">
<div id="grid"></div>
</p>

<br><br>
 
<p>The source below above is pretty self explanatory, feel free to post in the ExtJS forums with any questions as I will lurk there.</p>

<script src="http://gist.github.com/40145.js"></script>


Here is the javascript used to create the example above:

<script src="http://gist.github.com/411622.js"></script>
