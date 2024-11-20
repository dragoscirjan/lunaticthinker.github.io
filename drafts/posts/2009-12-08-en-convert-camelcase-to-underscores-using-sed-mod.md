---
title: Convert camelCase to Underscores Using sed (mod)
author: dragos
type: post
date: 2009-12-08T06:14:03+00:00
url: /index.php/en-convert-camelcase-to-underscores-using-sed-mod/
featured_image: http://lunaticthinker.me/wp-content/uploads/2009/12/bash.jpg
categories:
  - Linux in a Box
  - Uncategorized

---
This short post deals with converting strings of the form camelCase or CamelCase into camel_case, and vice versa. This is an update for the article [Convert camelCase to Underscores Using sed][1].<!--more-->

I discovered Amir did not think that variables may have numbers in them, so here it is a more complete version, I hope:

Convert CamelCase or camelCase to camel_case:

<pre class="prettyprint">sed -e 's/([A-Z0-9])/_\l\1/g' -e 's/^_([a-z0-9])/\l\1/g' file.txt
echo "camelCase" | sed -e 's/([A-Z0-9])/_\l\1/g' -e 's/^_([a-z0-9])/\l\1/g'</pre>

Convert camel_case to camelCase

<pre class="prettyprint">sed -e 's/_([a-z0-9])/\u\1/g' file.txt
echo "camel_case" | sed -e 's/_([a-z0-9])/\u\1/g'</pre>

Convert camel_case to CamelCase:

<pre class="prettyprint">sed -e 's/_([a-z0-9])/\u\1/g' -e 's/^([a-z0-9])/\u\1/g' file.txt
echo "camel_case" | sed -e 's/_([a-z0-9])/\u\1/g' -e 's/^([a-z0-9])/\u\1/g'</pre>

Please notice the new _0-9_ element added to all regular expressions. You should still be careful of what variable you choose to transform considering there may be problems with variable naming rules for the programming language you use.

NOTE: Please note that I did not test all new formulas and they may not work. If they don&#8221;t or you have new modifications, please notify me.

[][2]

 [1]: http://dragosc.itmcd.ro/it-stuff/daily-minute/convert-camelcase-to-underscores-using-sed/ "Convert camelCase to Underscores Using sed"
 [2]: AmirWatad.com