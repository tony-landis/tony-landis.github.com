--- 
layout: posts
category: uncategorized
title: bullsEye - jQuery plugin to add interactive and visualization features to tables
tags: jquery plugin 
---

bullsEye - jQuery plugin to add interactive and visualization features to tables
================================================================================


This plugin converts a normal table with both row and column level headers to an somewhat interactive format - it is named bullsEye because the goal was to create a targeting effect when the mouse is being moved on the cells and row and column headings. Maybe something like "draw cell lines" or "table zoom" would have been more appropriate but I don't feel the need to agonize over names.

<img class="indent" src="http://github.com/tony-landis/jquery-plugins/raw/master/bullseye/screenshots/bullseye-hover.png" border="0" alt="" width="500" />


It was created so that large tables of data can be shown, and as the mouse is moved over the cells, visual effects are applied to the table so that the corresponding row and column header are highlighted so the current position within the table of data is revealed.

It also animates lines from the selected cell to the cell's corresponding row and column headers, making the identification of the cell's headers even easier to see.

When moving the mouse over a column or row header, it will highlight all intersecting cells and draw the line to them - this enables the quick identification of what cells are populated with data, as the application this plugin was developed for has intermittent data.

<img class="indent" src="http://github.com/tony-landis/jquery-plugins/raw/master/bullseye/screenshots/bullseye-row.png" border="0" alt="" width="500" />

So for example, if the mouse is moved over a column heading, only the cells in that column that are populated with data will be highlighted and lines will drawn from each those populated cells  to it's row heading for quick visual reference.

<img class="indent" src="http://github.com/tony-landis/jquery-plugins/raw/master/bullseye/screenshots/bullseye-col.png" border="0" alt="" width="500" />

The last bit of functionality is to either fade in or zoom in extra data on the cell level that is hidden by default when hovering over a cell.

The hover content can be HTML formatted and when it is revealed, the div used will automatically center over the selected cell and will be sized to align with total amount of cells configured - for example it can be 3 rows and 5 columns, or 3 row and 3 columns. This is visually appealing because the hover content will fit in the existing grid lines.

This is my first jQuery plugin - if anyone finds bugs or has usability suggestions let me know. I have tested it in Firefox, Opera, and Safari and the behaviors are pretty much identical.

If anyone wants to develop just fork the github repository and contact me if you have developed something you feel should be merged into the main release.

LINK: <a href="http://tony-landis.github.com/jquery-plugins/bullseye.html" target="_blank">bullsEye Live Example</a>

LINK: <a href="http://github.com/tony-landis/jquery-plugins/tree/master/bullseye/" target="_blank">bullsEye Github repository and Download</a>
