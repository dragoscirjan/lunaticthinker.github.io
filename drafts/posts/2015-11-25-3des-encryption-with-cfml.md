---
title: 3DES Encryption with CFML
author: dragos
type: post
date: 2015-11-25T14:13:21+00:00
url: /index.php/3des-encryption-with-cfml/
featured_image: http://lunaticthinker.me/wp-content/uploads/2014/03/dna-structure-cfml.png
categories:
  - Uncategorized
tags:
  - 3des
  - cf
  - cfml
  - coldfusion

---
Here&#8217;s a CFML version for the 3DES [encription script][1] I presented earlier (CFScript only).

<pre class="lang:cfml decode:true " >&lt;cffunction access="public" name="prepare3DESKey" returntype="string" output="false" hint="Prepare 3DES key for encryption.">
	&lt;cfargument name="key" type="string" default="" />
	&lt;cfif len(key) eq 0>
		&lt;cfset key = generateSecretKey('DESEDE') />
	&lt;cfelse>
		<!--- if key is lower than 24 bytes; fill the empty bytes with NULL --->
		&lt;cfloop from="#len(key)#" to="23" index="i">
			

<!--- do NOT use chr(0). in CF it does not work --->
			&lt;cfset key = key &#038; urlDecode('%00') />
		&lt;/cfloop>
	&lt;/cfif>
	&lt;cfreturn key />
&lt;/cffunction>

&lt;cfset key="#prepare3DESKey('62v01fVsCWHfRcW')#" />
&lt;cfdump var="#len(key)#" />
&lt;cfdump var="#key#" />

&lt;cffunction access="public" name="encrypt3DESCBC" returntype="string" output="false" hint="Encrypts a string based on a given key and initialization vector, using CF methods.">
	&lt;cfargument name="encrypting" type="string" required="true" />
	&lt;cfargument name="key"        type="string" default="" />
	&lt;cfargument name="iv"         type="string" default="0000000000000000" />

	&lt;cfreturn encrypt(
		encrypting,
		toBase64(prepare3DESKey(key)),
		'DESEDE/CBC/PKCS5Padding', 
        'Base64', 
        BinaryDecode(iv, 'Hex')
	) />
&lt;/cffunction>

&lt;cffunction access="public" name="encrypt3DESCBC_Java" returntype="string" output="false" hint="Encrypts a string based on a given key and initialization vector, using Java functionality.">
	&lt;cfargument name="encrypting" type="string" required="true" />
	&lt;cfargument name="key"        type="string" default="" />
	&lt;cfargument name="iv"         type="string" default="0000000000000000" />
	
	&lt;cfset iv = BinaryDecode(iv, 'Hex') />

	&lt;cfset Cipher          = createObject('java', 'javax.crypto.Cipher') />
	&lt;cfset jcipher = Cipher.getInstance("DESede/CBC/PKCS5Padding") />
	
    &lt;cfset IvParameterSpec = createObject('java', 'javax.crypto.spec.IvParameterSpec') />
    &lt;cfset jiv = IvParameterSpec.init(iv) />
    
    &lt;cfset SecretKeySpec   = createObject('java', 'javax.crypto.spec.SecretKeySpec') />
    &lt;cfset jkey = SecretKeySpec.init(
        prepare3DESKey(key).getBytes(), 
        "DESede"
    ) />

    &lt;cfset jcipher.init(Cipher.ENCRYPT_MODE, jkey, jiv) />
                
    &lt;cfreturn toBase64(jcipher.doFinal(
        encrypting.getBytes()
    )) />
    
    &lt;cfreturn "test" />
&lt;/cffunction>

&lt;cfdump var="#encrypt3DESCBC('66866996699', '62v01fVsCWHfRcW')#" /></pre>

And here&#8217;s a test script [on trycf.com][2] also.

 [1]: /coders-grave/web-develop/coldfusion/3des-encryption-with-cfscript/
 [2]: http://trycf.com/gist/36a929d67b1a95897667/acf11?theme=monokai