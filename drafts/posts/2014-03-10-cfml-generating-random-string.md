---
title: CFML Generating Random String
author: dragos
type: post
date: 2014-03-10T10:27:40+00:00
url: /index.php/cfml-generating-random-string/
featured_image: http://lunaticthinker.me/wp-content/uploads/2014/03/dna-structure-cfml.png
categories:
  - "Coder's Grave"
  - ColdFusion
  - Home Page

---
While working on one of my client&#8217;s projects, I&#8217;ve encountered the need of generating RANDOM strings. Since, as almost, any other language, Cold Fusion does not have any type of support for this functionality, I had to create a function of my own.

Hope you find it useful.<!--more-->

<pre class="prettyprint">&#60;cfscript&#62;
    function RandomString(length, chars="ABCDEFGHIJKLMNOPQRST0123456789-") {
        var i = 0;
        var theString = "";
        for (i = 0; i &lt; length; i++) {
            theString &#038;= Mid(chars, RandRange(1, len(chars), "SHA1PRNG"), 1);
        }
        return theString;
    }
&#60;/cfscript&#62;</pre>