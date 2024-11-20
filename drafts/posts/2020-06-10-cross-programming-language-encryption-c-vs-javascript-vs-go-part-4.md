---
title: 'Cross Programming Language Encryption – C# vs JavaScript vs Go, Part 4'
author: admin
type: post
date: 2020-06-10T19:59:11+00:00
url: /index.php/cross-programming-language-encryption-c-vs-javascript-vs-go-part-4/
featured_image: http://lunaticthinker.me/wp-content/uploads/2015/11/dna-structure.jpg
eltd_featured_post_meta:
  - no
eltd_disable_footer_meta:
  - no
eltd_video_type_meta:
  - social
eltd_hide_background_image_meta:
  - no
categories:
  - "Coder's Grave"
  - GO
  - Home Page
  - JavaScript

---
[![GitHub stars][1]][2] [![GitHub stars][3]][4] [![GitHub stars][5]][6] 

[![GitHub followers][7]][8]  
See a demo at [Github][9]

* * *

This 4th section of the article was written about a week after I finished the other 3 to clarify a mistake I kinda perpetuated from the begging. After reviewing my code, one of my colleagues raised the following issue: _even though I was using RSA encryption, I was using the wrong public and private key_.

So I had to go back, to the beginning:

### RSA

**_RSA_**_&nbsp;(_**_Rivest–Shamir–Adleman_**_) is one of the first&nbsp;_[_public-key cryptosystems_][10]_&nbsp;and is widely used for secure data transmission. In such a&nbsp;_[_cryptosystem_][11]_, the&nbsp;_[_encryption key_][12]_&nbsp;is public and distinct from the&nbsp;_[_decryption key_][13]_&nbsp;which is kept secret (private). In RSA, this asymmetry is based on the practical difficulty of&nbsp;_[_factoring_][14]_&nbsp;the product of two large&nbsp;_[_prime numbers_][15]_, the &#8220;_[_factoring problem_][16]_&#8220;. The&nbsp;_[_acronym_][17]_&nbsp;RSA is the initial letters of the surnames of&nbsp;_[_Ron Rivest_][18]_,&nbsp;_[_Adi Shamir_][19]_, and&nbsp;_[_Leonard Adleman_][20]_, who publicly described the algorithm in 1977.&nbsp;_[_Clifford Cocks_][21]_, an English mathematician working for the British intelligence agency&nbsp;_[_Government Communications Headquarters_][22]_&nbsp;(GCHQ), had developed an equivalent system in 1973, which was not&nbsp;_[_declassified_][23]_&nbsp;until 1997._

I will not go back into the entire content of the first part of the article and I will try to resume. Basically, instead of using an RSA public and private key, I was using an X.509 generated certificate. 

So what&#8217;s wrong with using a DI & a PKI?

### Digital Identity & PKI

_A&nbsp;_**_digital identity_**_&nbsp;is information on an entity used by&nbsp;_[_computer systems_][24]_&nbsp;to represent an external agent. That agent may be a person, organization, application, or device. ISO/IEC 24760-1 defines identity as &#8220;set of attributes related to an entity&#8221;._<sup><a href="https://en.wikipedia.org/wiki/Digital_identity#cite_note-1"><em>[1]</em></a></sup>

_The information contained in a digital identity allows for assessment and authentication of a user interacting with a business system on the web, without the involvement of human operators. Digital identities allow our access to computers and the services they provide to be automated, and make it possible for computers to mediate relationships._

_The term &#8220;digital identity&#8221; also denotes certain aspects of civil and personal identity that have resulted from the widespread use of identity information to represent people in an acceptable trusted digital format in computer systems._

_A&nbsp;_**_public key infrastructure_**_&nbsp;(_**_PKI_**_) is a set of roles, policies, hardware, software and procedures needed to create, manage, distribute, use, store and revoke&nbsp;_[_digital certificates_][25]_&nbsp;and manage public-key encryption. The purpose of a PKI is to facilitate the secure electronic transfer of information for a range of network activities such as e-commerce, internet banking and confidential email. It is required for activities where simple passwords are an inadequate authentication method and more rigorous proof is required to confirm the identity of the parties involved in the communication and to validate the information being transferred._

_In&nbsp;_[_cryptography_][26]_, a PKI is an arrangement that&nbsp;binds&nbsp;_[_public keys_][27]_&nbsp;with respective identities of entities (like people and organizations). The binding is established through a process of registration and issuance of certificates at and by a&nbsp;_[_certificate authority_][28]_&nbsp;(CA). Depending on the assurance level of the binding, this may be carried out by an automated process or under human supervision._

_The PKI role that assures valid and correct registration is called a&nbsp;registration authority&nbsp;(RA). An RA is responsible for accepting requests for digital certificates and authenticating the entity making the request.&nbsp;In a&nbsp;_[_Microsoft_][29]_&nbsp;PKI, a registration authority is usually called a subordinate CA._

### X.509

_In&nbsp;_[_cryptography_][26]_,&nbsp;_**_X.509_**_&nbsp;is a standard defining the format of&nbsp;_[_public key certificates_][30]_._<sup><a href="https://en.wikipedia.org/wiki/X.509#cite_note-1"><em>[1]</em></a></sup>_&nbsp;X.509 certificates are used in many Internet protocols, including&nbsp;_[_TLS/SSL_][31]_, which is the basis for HTTPS_<sup><a href="https://en.wikipedia.org/wiki/X.509#cite_note-:0-2"><em>[2]</em></a></sup>_, the secure protocol for browsing the&nbsp;_[_web_][32]_. They are also used in offline applications, like&nbsp;_[_electronic signatures_][33]_. An X.509 certificate contains a public key and an identity (a hostname, or an organization, or an individual), and is either signed by a&nbsp;_[_certificate authority_][28]_&nbsp;or self-signed. When a certificate is signed by a trusted certificate authority, or validated by other means, someone holding that certificate can rely on the public key it contains to establish secure communications with another party, or validate documents&nbsp;_[_digitally signed_][34]_&nbsp;by the corresponding&nbsp;_[_private key_][35]_._

_X.509 also defines&nbsp;_[_certificate revocation lists_][36]_, which are a means to distribute information about certificates that have been deemed invalid by a signing authority, as well as a&nbsp;_[_certification path validation algorithm_][37]_, which allows for certificates to be signed by intermediate CA certificates, which are, in turn, signed by other certificates, eventually reaching a&nbsp;_[_trust anchor_][38]_._

The answer is somewhere in the middle. I could complete my colleague&#8217;s observation like this: _you&#8217;re using the wrong certificate X.509 has an expiration date, while RSA public and private key don&#8217;t._ 

We were planning on delivering this application to be used for a very long time. It is very hard for us to deliver and change the encryption and decryption keys, thus using a simple RSA public & private key would benefit way more than passing on an SSL certificate.

### Conclusion

RSA is&nbsp;_two_&nbsp;algorithms, one for asymmetric encryption, the other one for digital signatures. They use the same kind of keys, they share the same core operation, and they are both called &#8220;RSA&#8221;.

Diffie-Hellman is a key exchange algorithm; you can view it as a kind of asymmetric encryption algorithm where you do not get to choose what you encrypt. This is fine for key exchange, where you just want to obtain an essentially random shared secret between two people. Note that most usages of RSA asymmetric encryption, in practice, are also key exchange, e.g. in SSL/TLS: the client generates a random value, encrypts it with the server&#8217;s public key, and send it to the server.

When it comes to SSL/TLS communication, an RSA certificate is generated like this:

<pre class="wp-block-code"><code lang="bash" class="language-bash">openssl genrsa -out cert/rsa/key.pem 2048
openssl rsa -in cert/rsa/key.pem -pubout -out cert/rsa/cert.pem</code></pre>

When you organize certificates in a way such that there is a strict hierarchy, where certificate issuers are called&nbsp;_Certification Authorities_&nbsp;and issue certificates to each other, with a handful of top-CA called &#8220;root CA&#8221;, then that overall structure is called a&nbsp;_Public Key Infrastructure_, i.e. a PKI.

X.509 is a standard for the format and contents of certificates. X.509 is rather open about what signature algorithms will be used for signing certificates, but in practice, 99% of the time, it will be RSA.

Another difference would be that an X.509 certificate, must and will always have an expiry date.

<pre class="wp-block-code"><code lang="bash" class="language-bash">openssl req -newkey rsa:2048 -nodes -keyout cert/x509/key.pem -x509 -days 365 -out cert/x509/cert.pem</code></pre>

**All the demo repositories presented within this article, have been updated to show you how to implement encryption with both a simple RSA public and private key, and also using a X.509 certificate and private key.**

### References

  * [Digital Identity][39]
  * [Public Key Infrastructure][40] 
  * [Relationship between RSA, Diffie-Hellman Key Exchange, PKI and X.509?][41] 
  * [Making sense of SSL, RSA, X509 and CSR][42] 

### Related Articles

  * [Cross Programming Language Encryption &#8211; CSharp, Part 1][43] 
  * [Cross Programming Language Encryption – NodeJs vs Go, Part 3][44]
  * [Cross Programming Language Encryption – C# vs NodeJs vs Go, Part 4][45]

 [1]: https://img.shields.io/github/stars/lunaticthinker-me/demo-cross-lang-encryption-cs?label=Crypt%20C%23%20Demo&style=social
 [2]: https://github.com/lunaticthinker-me/demo-cross-lang-encryption-cs
 [3]: https://img.shields.io/github/stars/lunaticthinker-me/demo-cross-lang-encryption-go?label=Crypt%20Go%20Demo&style=social
 [4]: https://github.com/lunaticthinker-me/demo-cross-lang-encryption-go
 [5]: https://img.shields.io/github/stars/lunaticthinker-me/demo-cross-lang-encryption-js?label=Crypt%20NodeJs%20Demo&style=social
 [6]: https://github.com/lunaticthinker-me/demo-cross-lang-encryption-js
 [7]: https://img.shields.io/github/followers/dragoscirjan?style=social
 [8]: https://github.com/dragoscirjan
 [9]: https://github.com/lunaticthinker-me
 [10]: https://en.wikipedia.org/wiki/Public-key_cryptography
 [11]: https://en.wikipedia.org/wiki/Cryptosystem
 [12]: https://en.wikipedia.org/wiki/Encryption_key
 [13]: https://en.wikipedia.org/wiki/Decryption_key
 [14]: https://en.wikipedia.org/wiki/Factorization
 [15]: https://en.wikipedia.org/wiki/Prime_number
 [16]: https://en.wikipedia.org/wiki/Factoring_problem
 [17]: https://en.wikipedia.org/wiki/Acronym
 [18]: https://en.wikipedia.org/wiki/Ron_Rivest
 [19]: https://en.wikipedia.org/wiki/Adi_Shamir
 [20]: https://en.wikipedia.org/wiki/Leonard_Adleman
 [21]: https://en.wikipedia.org/wiki/Clifford_Cocks
 [22]: https://en.wikipedia.org/wiki/Government_Communications_Headquarters
 [23]: https://en.wikipedia.org/wiki/Classified_information
 [24]: https://en.wikipedia.org/wiki/Computer
 [25]: https://en.wikipedia.org/wiki/Digital_certificates
 [26]: https://en.wikipedia.org/wiki/Cryptography
 [27]: https://en.wikipedia.org/wiki/Public_key
 [28]: https://en.wikipedia.org/wiki/Certificate_authority
 [29]: https://en.wikipedia.org/wiki/Microsoft
 [30]: https://en.wikipedia.org/wiki/Public_key_certificate
 [31]: https://en.wikipedia.org/wiki/Transport_Layer_Security
 [32]: https://en.wikipedia.org/wiki/World_Wide_Web
 [33]: https://en.wikipedia.org/wiki/Electronic_signature
 [34]: https://en.wikipedia.org/wiki/Digital_signature
 [35]: https://en.wikipedia.org/wiki/Private_key
 [36]: https://en.wikipedia.org/wiki/Certificate_revocation_list
 [37]: https://en.wikipedia.org/wiki/Certification_path_validation_algorithm
 [38]: https://en.wikipedia.org/wiki/Trust_anchor
 [39]: https://en.wikipedia.org/wiki/Digital_identity#:~:text=A%20digital%20identity%20is%20information,attributes%20related%20to%20an%20entity%22.
 [40]: https://en.wikipedia.org/wiki/Public_key_infrastructure
 [41]: https://security.stackexchange.com/questions/80853/relationship-between-rsa-diffie-hellman-key-exchange-pki-and-x-509
 [42]: https://blog.gisspan.com/2016/04/making-sense-of-ssl-rsa-x509-and-csr.html
 [43]: https://lunaticthinker.me/index.php/cross-programming-language-encryption-csharp-part-1/
 [44]: https://lunaticthinker.me/index.php/cross-programming-language-encryption-javascript-vs-go-part-3/
 [45]: /index.php/cross-programming-language-encryption-c-vs-javascript-vs-go-part-4/