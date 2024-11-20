---
title: IP/TCP/UDP header vs C/C++
author: dragos
type: post
date: 2008-04-09T23:28:43+00:00
url: /index.php/iptcpudp-header-vs-cc/
eltd_hide_background_image_meta:
  - no
eltd_video_type_meta:
  - social
eltd_disable_footer_meta:
  - no
eltd_featured_post_meta:
  - no
categories:
  - C...
  - "Coder's Grave"
  - Home Page

---
NOTE: This article is written in Romanian, for a translation please add me a comment.

Ei bine&#8230; nu mare imi fu uimirea dupa ultimile proiecte / teme, mici aplicatii. Uimirea ca habar nu am sa scriu un header tcp. Toata povestea a plecat de la o tema pentru facultate. Un modul de kernel linux/windows care trebuia sa filtreze pachetele TCP si UDP.<!--more-->

Asa ca&#8230; mai dupa explicatiile unui coleg, mai cu burta pe wikipedia & others&#8230; am priceput cum arata headerele pentru fiecare element mentionat mai sus, si&#8230; simt nevoia sa impartasesc si celorlalti, ca poate voua v-o fi mai usor decat mine alta data.

Dar am sa va trec mai intai prin header-ul ipv4, pentru ca trebuie sa stiti si despre el.

**IP Header**
  
Una bucata varianta ar fi astfel:

<pre class="prettyprint">struct iphdr {
    unsigned char        header_length:5, version:4; // nu stiu de ce am vaga impresie ca acel cinci e de fapt 4 :(
    unsigned char        tos;
    unsigned short int   length;
    unsigned short int   id;
    unsigned char        flags;
    unsigned short int   offset;
    unsigned char        ttl;
    unsigned char        protocl;
    unsigned short int  checksum;
    unsigned int          id_src;
    unsigned int          ip_dst;
};
</pre>

care separa header length de version, si care poate fi mult mai folositoare in cazul in care aveti nevoie de cele doua variabile.
  
In caz contrar, recomand

<pre class="prettyprint">struct iphdr
{
    UCHAR     version;	   // Version and length
    UCHAR     tos;		    // Type of service
    USHORT    length;	  // Total datagram length
    USHORT    id;		    // Identification
    USHORT    flags;	   // Flags
    UCHAR     ttl;		     // Time to live
    UCHAR     protocol;	  // Protocol
    USHORT    checksum;    // Header checksum
    ULONG     source;	  // Source address
    ULONG     dest;		   // Destination address
};
</pre>

Ca studiu de caz ca ofer structura ipdhr extrasa din Linux kernel 2.6.24.2:

<pre class="prettyprint">struct iphdr {
#if defined(__LITTLE_ENDIAN_BITFIELD)
    __u8    ihl:4, version:4;
#elif defined (__BIG_ENDIAN_BITFIELD)
    __u8    version:4, ihl:4;
#else
#error  "Please fix "
#endif
    __u8    tos;
    __be16  tot_len;
    __be16  id;
    __be16  frag_off;
    __u8    ttl;
    __u8    protocol;
    __sum16 check;
    __be32  saddr;
    __be32  daddr;
    /*The options start here. */
};
</pre>

cu urmatoarele definitii:

<pre class="prettyprint">typedef unsigned char __u8;
    typedef __u16 __bitwise __be16;     //  typedef unsigned short __u16;
    typedef __u16 __bitwise __sum16;   //  (despre __bitwise mai caut)
    typedef __u32 __bitwise __be32;     //  typedef unsigned int __u32;
</pre>

Referinte:
  
+ <a href="http://en.wikipedia.org/wiki/IPv4" target="_blank" rel="noopener noreferrer">http://en.wikipedia.org/wiki/IPv4</a>
  
+ <a href="http://www.codeproject.com/KB/IP/drvfltip.aspx" target="_blank" rel="noopener noreferrer">http://www.codeproject.com/KB/IP/drvfltip.aspx</a>

**TCP Header**

Despre headerul tcp, nu prea are rost sa va povestesc eu, cand veti putea gasit explicat totul f.f.f.f. bine aici: [http://en.wikipedia.org/wiki/Transmission\_Control\_Protocol][1]

Ca si la header-ul ip, va ofer mai intai o varianta detaliata, in asa fel incat se poate prelua fiecare componenta in parte.

<pre class="prettyprint">struct tcphdr
{
    USHORT	sourcePort;		 // Source Port
    USHORT	destinationPort;	// Destination Port
    ULONG	nSequence;		// Number of Sequence
    ULONG	nAck;			   // Number of aknowledge
    UCHAR	unused:4;		 // Unused
    UCHAR	offset:4;		   // Data offset
    UCHAR	fin:1, syn:1, rst:1, psh:1, ack:1, urg:1, ece:1, cwr:1; // Flags
    USHORT	window;			 // Size of window
    USHORT	checksum;		// Total checksum
    USHORT	urp;			    // Urgent pointer
};
</pre>

Daca insa NU ne intereseaza lucrul acesta, recomand versiunea urmatoare,

<pre class="prettyprint">struct tcphdr
{
    USHORT			source;		    // Source Port
    USHORT			dest;		     // Destination Port
    ULONG			 seq;		      // Number of Sequence
    ULONG			 nack;		      // Number of aknowledge
    UCHAR			 dataoffset;        // Pointer to data
    UCHAR			 flags;		       // Flags
    USHORT			windows;	   // Size of window
    USHORT			checksum;	  // Total checksum
    USHORT			urgentPointer;	  // Urgent pointer
};
</pre>

luand in calcul faptul ca pentru flags putem face teste cu expresii de tipul:

<pre class="prettyprint">((struct tcphdr *)-&gt;flags & 0x01) // pentru FIN
// #define CWR 128 // 0x80
// #define ECE 64   // 0x40
// #define URG 32   // 0x20
// #define ACK 16   // 0x10
// #define PSH 8    // 0x08
// #define RST 4    // 0x04
// #define SYN 2    // 0x02
// #define FIN 1     // 0x01
</pre>

Din nou, ca stiudiu de caz &#8211; strucutra tcphdr din Linux kernel:

<pre class="prettyprint">struct tcphdr {
    __be16  source;
    __be16  dest;
    __be32  seq;
    __be32  ack_seq;
#if defined(__LITTLE_ENDIAN_BITFIELD)
    __u16   res1:4, doff:4, fin:1, syn:1, rst:1, psh:1, ack:1, urg:1, ece:1, cwr:1;
#elif defined(__BIG_ENDIAN_BITFIELD)
    __u16   doff:4,
res1:4, cwr:1, ece:1, urg:1, ack:1, psh:1, rst:1, syn:1, fin:1;
#else
#error  "Adjust your  defines"
#endif
     __be16  window;
     __sum16 check;
     __be16  urg_ptr;
};
</pre>

Referinte:
  
+ [http://en.wikipedia.org/wiki/Transmission\_Control\_Protocol][1]
  
+ <a href="http://www.codeproject.com/KB/IP/drvfltip.aspx" target="_blank" rel="noopener noreferrer">http://www.codeproject.com/KB/IP/drvfltip.aspx</a>
  
+ <http://www.tenouk.com/Module43b.html>

**UDPHeader**

Ca ultima tema, headerul UDP, explicat iarasi, pe indelete in aceasta pagina: <a href="http://en.wikipedia.org/wiki/User_Datagram_Protocol" target="_blank" rel="noopener noreferrer">http://en.wikipedia.org/wiki/User_Datagram_Protocol</a>,
  
care este insa mult mai simplu, si mult mai usor de transpus in cod:

<pre class="prettyprint">struct udphdr
{
    USHORT		source;		// Source Port
    USHORT		dest;		// Destination Port
    USHORT		len;		// Total length
    USHORT		checksum;	// Total checksum
};
</code>

si ca studiu de caz - struct udphdr din Linux Kernel:



<pre class="prettyprint">
struct udphdr {
    __be16  source;
    __be16  dest;
    __be16  len;
    __sum16 check;
};
</pre>


<p>
  Referinte:<br />
  + <a href="http://en.wikipedia.org/wiki/User_Datagram_Protocol">http://en.wikipedia.org/wiki/User_Datagram_Protocol</a><br />
  + <a href="http://www.codeproject.com/KB/IP/drvfltip.aspx" target="_blank" rel="noopener noreferrer">http://www.codeproject.com/KB/IP/drvfltip.aspx</a><br />
  + <a href="http://www.tenouk.com/Module43b.html">http://www.tenouk.com/Module43b.html</a>
</p>

 [1]: http://en.wikipedia.org/wiki/Transmission_Control_Protocol