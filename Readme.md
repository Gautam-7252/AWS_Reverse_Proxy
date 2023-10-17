

<head>
<meta http-equiv=Content-Type content="text/html; charset=utf-8">
<meta name=Generator content="Microsoft Word 15 (filtered)">
</head>

<body lang=EN-US style='word-wrap:break-word'>

<div class=WordSection1>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'><br>
<br>
</span></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:18.0pt;font-family:"Times New Roman",serif;
color:black'>Abstract&nbsp;</span></u></b></p>
<br>
<p class=MsoNormal style='margin-top:0in;margin-right:5.85pt;margin-bottom:
0in;margin-left:5.0pt;text-align:justify;text-justify:inter-ideograph;
text-indent:31.0pt;line-height:normal'><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black'>Our project mainly focused on
designing of </span><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:#333333;background:white'>reverse proxy. It employs cookie as its ticket,
so it can easily handle the user identification of enterprise. This paper
discusses how to revise the transmitting cookie value of Reverse proxy and
adjust it to the communication between background server and client entry, in
order to maintain the effective cookie session between the client and the
server. The paper analyzes the reason for the cookie is easy to attack, and
works out a scheme of relaying the secure session, which covers the defect in
protecting cookie.</span></p>
<br>
<br>
<br>
<br>
<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>Introduction</span></u></b></p>
<br>
<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-family:"Times New Roman",serif;color:#222222'>A reverse proxy is a
server that sits in front of web servers and forwards client (e.g. web browser)
requests to those web servers. Reverse proxies are typically implemented to
help increase&nbsp;</span><a
href="https://www.cloudflare.com/learning/security/what-is-web-application-security/"><span
style='font-family:"Times New Roman",serif;color:#0051C3'>security</span></a><span
style='font-family:"Times New Roman",serif;color:#222222'>,&nbsp;</span><a
href="https://www.cloudflare.com/learning/performance/why-site-speed-matters/"><span
style='font-family:"Times New Roman",serif;color:#0051C3'>performance</span></a><span
style='font-family:"Times New Roman",serif;color:#222222'>, and reliability. In
order to better understand how a reverse proxy works and the benefits it can
provide, let’s first define what a proxy server is.</span></p>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-family:"Times New Roman",serif;color:#222222'>A forward proxy,
often called a proxy, proxy server, or web proxy, is a server that sits in
front of a group of client machines. When those computers make requests to
sites and services on the Internet, the proxy server intercepts those requests
and then communicates with web servers on behalf of those clients, like a
middleman.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:0in;line-height:150%'><span style='font-size:12.0pt;line-height:
150%;font-family:"Times New Roman",serif;color:#222222'>For example, let’s name
3 computers involved in a typical forward proxy communication:</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:#222222'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>A: This is a user’s home computer</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:#222222'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>B: This is a forward proxy server</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:#222222'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>C: This is a website’s origin server (where the website data is
stored)</span></p>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><img
border=0 width=599 height=261 id=rectole0000000000
src="aws%20report_files/image001.jpg"></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:0in;line-height:150%'><span style='font-size:12.0pt;line-height:
150%;font-family:"Times New Roman",serif;color:#222222'>In a standard Internet
communication, computer A would reach out directly to computer C, with the
client sending requests to the&nbsp;</span><a
href="https://www.cloudflare.com/learning/cdn/glossary/origin-server/"><span
style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#0051C3'>origin server</span></a><span style='font-size:12.0pt;
line-height:150%;font-family:"Times New Roman",serif;color:#222222'>&nbsp;and
the origin server responding to the client. When a forward proxy is in place, A
will instead send requests to B, which will then forward the request to C. C
will then send a response to B, which will forward the response back to A.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:0in;line-height:150%'><span style='font-size:12.0pt;line-height:
150%;font-family:"Times New Roman",serif;color:#222222'>The difference between
a forward and reverse proxy is subtle but important. A simplified way to sum it
up would be to say that a forward proxy sits in front of a client and ensures
that no origin server ever communicates directly with that specific client. On
the other hand, a reverse proxy sits in front of an origin server and ensures
that no client ever communicates directly with that origin server.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:0in;line-height:150%'><span style='font-size:12.0pt;line-height:
150%;font-family:"Times New Roman",serif;color:#222222'>Once again, let’s
illustrate by naming the computers involved:</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:#222222'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>D: Any number of users’ home computers</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:#222222'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>E: This is a reverse proxy server</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:#222222'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>F: One or more origin servers</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><img border=0
width=599 height=273 id=rectole0000000001 src="aws%20report_files/image002.jpg"></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:0in;line-height:150%'><span style='font-size:12.0pt;line-height:
150%;font-family:"Times New Roman",serif;color:#222222'>Typically all requests
from D would go directly to F, and F would send responses directly to D. With a
reverse proxy, all requests from D will go directly to E, and E will send its
requests to and receive responses from F. E will then pass along the
appropriate responses to D.</span></p>
<br>
<br>
<br>
<br>
<p class=MsoNormal align=center style='margin-top:5.0pt;margin-right:0in;
margin-bottom:5.0pt;margin-left:0in;text-align:center;line-height:150%'><b><u><span
style='font-size:14.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'>Problem Statement</span></u></b></p>

<p class=MsoNormal align=center style='margin-top:5.0pt;margin-right:0in;
margin-bottom:5.0pt;margin-left:0in;text-align:center;line-height:150%'><b><u><span
style='font-size:14.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:#222222'><span style='text-decoration:none'>&nbsp;</span></span></u></b></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:.5in;text-indent:-.25in;line-height:150%'><span style='font-size:
13.5pt;line-height:150%;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:13.5pt;line-height:150%;font-family:"Times New Roman",serif;
color:black;background:white'>If an excessive amount of internet traffic is
slowing down your system, you can use load balancing, which distributes your
traffic over one or more servers to improve overall performance.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:.5in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:black;background:white'>When your internal services are hidden from
public view, it’s easier to remove services, add new ones, upgrade them, or
roll them back.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:5.0pt;
margin-left:.5in;text-indent:-.25in;line-height:150%'><span style='font-size:
12.0pt;line-height:150%;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><span style='font-size:12.0pt;line-height:150%;font-family:"Times New Roman",serif;
color:black;background:white'>Businesses commonly host their website’s content
management system or shopping cart apps with an external service outside their
own network. Rather than tell site visitors that you’re sending them to another
URL for payment, you can use a reverse proxy to conceal that detail. A reverse
proxy can hide the presence of external vendors, such as your cloud service
provider, who provide components of your customer experience.</span></p>
<br>
<br>
<br>
<br>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>Flowchart</span></u></b></p>
<br>
<br>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><img border=0 width=599 height=636 id=rectole0000000002
src="aws%20report_files/image003.jpg"></p>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'><br>
<br>
<br>
</span></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>AWS</span></u></b></p>

<br>
<br>

<p class=MsoNormal style='margin-top:6.0pt;margin-right:0in;margin-bottom:6.0pt;
margin-left:0in;line-height:normal'><b><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>Amazon Web
Services, Inc.</span></b><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black;background:white'>&nbsp;(<b>AWS</b>) is a subsidiary of&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Amazon.com"><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>Amazon</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>&nbsp;that provides&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Software_as_a_service"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>on-demand</span></a><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black;background:white'>&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Cloud_computing"><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>cloud
computing</span></a><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black;background:white'>&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Computing_platform"><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>platforms</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>&nbsp;and&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Application_programming_interface"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>APIs</span></a><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black;background:white'>&nbsp;to individuals,
companies, and governments, on a metered pay-as-you-go basis. These cloud
computing&nbsp;</span><a href="https://en.wikipedia.org/wiki/Web_services"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>web services</span></a><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>&nbsp;provide&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Distributed_computing"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>distributed computing</span></a><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>&nbsp;processing
capacity and software tools via AWS&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Server_farms"><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>server farms</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>. One of these services is&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>Amazon Elastic Compute Cloud</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>&nbsp;(EC2), which allows users to have at their disposal
a&nbsp;</span><a href="https://en.wikipedia.org/wiki/Virtualization"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>virtual</span></a><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black;background:white'>&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Computer_cluster"><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>cluster
of computers</span></a><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black;background:white'>, available all the time, through the Internet.
AWS's virtual computers emulate most of the attributes of a real computer,
including hardware&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Central_processing_unit"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>central processing units</span></a><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>&nbsp;(CPUs)
and&nbsp;</span><a href="https://en.wikipedia.org/wiki/Graphics_processing_unit"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>graphics processing units</span></a><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>&nbsp;(GPUs)
for processing; local/</span><a
href="https://en.wikipedia.org/wiki/Random-access_memory"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>RAM</span></a><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black;background:white'>&nbsp;memory; hard-disk/</span><a
href="https://en.wikipedia.org/wiki/Solid-state_drive"><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>SSD
storage</span></a><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black;background:white'>; a choice of operating systems; networking; and
pre-loaded application software such as&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Web_server"><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>web servers</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>,&nbsp;</span><a href="https://en.wikipedia.org/wiki/Database"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>databases</span></a><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black;background:white'>, and&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Customer_relationship_management"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>customer relationship management</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>&nbsp;(CRM).</span></p>

<p class=MsoNormal style='margin-top:6.0pt;margin-right:0in;margin-bottom:6.0pt;
margin-left:0in;line-height:normal'><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black;background:white'>AWS services are
delivered to customers via a network of AWS server farms located throughout the
world. Fees are based on a combination of usage (known as a
&quot;Pay-as-you-go&quot; model), hardware, operating system, software, or
networking features chosen by the subscriber required&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Availability_(system)"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>availability</span></a><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>,&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Redundancy_(engineering)"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>redundancy</span></a><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>,&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Computer_security"><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black;background:white'>security</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>, and service options. Subscribers can pay for a single
virtual AWS computer, a dedicated physical computer, or clusters of either.
Amazon provides select portions of security for subscribers (e.g. physical security
of the data centers) while other aspects of security are the responsibility of
the subscriber (e.g. account management, vulnerability scanning, patching). AWS
operates from many global geographical regions including 6 in North America. </span></p>

<p class=MsoNormal style='margin-top:6.0pt;margin-right:0in;margin-bottom:6.0pt;
margin-left:0in;line-height:normal'><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black;background:white'>Amazon markets AWS to
subscribers as a way of obtaining large-scale computing capacity more quickly
and cheaply than building an actual physical server farm.&nbsp;All services are
billed based on usage, but each service measures usage in varying ways. As of
2021 Q4, AWS has 33% market share for cloud infrastructure while the next two
competitors&nbsp;</span><a href="https://en.wikipedia.org/wiki/Microsoft_Azure"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>Microsoft Azure</span></a><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>&nbsp;and&nbsp;</span><a
href="https://en.wikipedia.org/wiki/Google_Cloud_Platform"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black;
background:white'>Google Cloud</span></a><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black;background:white'>&nbsp;have
21%, and 10% respectively, according to Synergy Group. </span></p>

<p class=MsoNormal align=center style='margin-bottom:12.0pt;text-align:center;
line-height:normal'><span style='font-size:12.0pt;font-family:"Times New Roman",serif'>
<br>
<br>
<br>
<br>
</span><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif'>NGINX
– Reverse Proxy</span></u></b></p>
<br>
<br>

<p class=MsoNormal align=center style='margin-bottom:12.0pt;text-align:center;
line-height:normal'><span style='font-family:"Times New Roman",serif;
color:#5E6065;background:white'>A reverse proxy is a server that sits between
internal applications and external clients, forwarding client requests to the
appropriate server. While many common applications, such as Node.js, are able
to function as servers on their own, NGINX has a number of advanced load
balancing, security, and acceleration features that most specialized
applications lack. Using NGINX as a reverse proxy enables you to add these
features to any application.</span><b><u><span style='font-size:14.0pt;
font-family:"Times New Roman",serif'><br>
<br>
</span></u></p>

<p class=MsoNormal align=center style='margin-bottom:12.0pt;text-align:center;
line-height:normal'><span style='font-family:"Times New Roman",serif;
color:#5E6065;background:white'>Some common uses of NGINX as a reverse proxy
include load balancing to maximize server capacity and speed, cache commonly
requested content, and to act as an additional layer of security.</span><b><u><span
style='font-size:14.0pt;font-family:"Times New Roman",serif'><br>
<br>
</span></u></b></p>

<p class=MsoNormal align=center style='margin-bottom:12.0pt;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif'><span
 style='text-decoration:none'>&nbsp;</span></span></u></b></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>Implementation Steps</span></u></b></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'><span style='text-decoration:none'>&nbsp;</span></span></u></b></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><span style='font-size:14.0pt;font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'>Step  1:-
Launching an Instance with name NGINX.</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000003
src="aws%20report_files/image004.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>Step 2:-
Created a key pair.</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000004
src="aws%20report_files/image005.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>Step 3:-
Instance launched successfully.</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000005
src="aws%20report_files/image006.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-size:12.0pt;line-height:107%;font-family:
"Times New Roman",serif'>Step 4:- Installing NGINX in the Instance using linux
terminal.</span></p>

<p class=MsoNormal><span style='font-size:12.0pt;line-height:107%;font-family:
"Times New Roman",serif'>Commands used to install NGINX :-</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:10.5pt;font-family:"Times New Roman",serif;color:#5E6065;
letter-spacing:.75pt;background:#EDEDF4'>sudo wget </span><a
href="http://nginx.org/keys/nginx_signing.key"><span style='font-size:10.5pt;
font-family:"Times New Roman",serif;color:#5E6065;letter-spacing:.75pt;
background:#EDEDF4'>http://nginx.org/keys/nginx_signing.key</span></a></p>

<p class=MsoNormal><span style='font-size:10.5pt;line-height:107%;font-family:
"Times New Roman",serif;color:#5E6065;letter-spacing:.75pt;background:#EDEDF4'>sudo
apt-key add nginx_signing.key</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:10.5pt;font-family:"Times New Roman",serif;color:#5E6065;
letter-spacing:.75pt;background:#EDEDF4'>sudo apt update</span></p>

<p class=MsoNormal><span style='font-size:10.5pt;line-height:107%;font-family:
"Times New Roman",serif;color:#5E6065;letter-spacing:.75pt;background:#EDEDF4'>sudo
apt install nginx</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:10.5pt;font-family:"Times New Roman",serif;color:#5E6065;
letter-spacing:.75pt;background:#EDEDF4'>sudo systemctl start nginx</span></p>

<p class=MsoNormal><span style='font-size:10.5pt;line-height:107%;font-family:
"Times New Roman",serif;color:#5E6065;letter-spacing:.75pt;background:#EDEDF4'>sudo
systemctl enable nginx</span></p>

<p class=MsoNormal><span style='font-size:12.0pt;line-height:107%;font-family:
"Times New Roman",serif'>Message which shows the NGINX is now active :-</span></p>

<p class=MsoNormal style='margin-top:12.0pt;margin-right:0in;margin-bottom:
12.0pt;margin-left:0in;line-height:normal'><span style='font-size:11.5pt;
font-family:"Times New Roman",serif;color:#003366;background:#EEEEEE'>sudo
systemctl status nginx</span></p>

<p class=MsoNormal style='margin-top:12.0pt;margin-right:0in;margin-bottom:
12.0pt;margin-left:0in;line-height:normal'><b><span style='font-size:11.5pt;
font-family:"Segoe UI Symbol",sans-serif;color:green;background:#EEEEEE'>●</span></b><b><span
style='font-size:11.5pt;font-family:"Times New Roman",serif;color:green;
background:#EEEEEE'> nginx</span></b><span style='font-size:11.5pt;font-family:
"Times New Roman",serif;color:#003366;background:#EEEEEE'> </span><span
style='font-size:11.5pt;font-family:"Times New Roman",serif;color:gray;
background:#EEEEEE'>- A high performance web, http and reverse proxy server</span></p>

<p class=MsoNormal style='margin-top:12.0pt;margin-right:0in;margin-bottom:
12.0pt;margin-left:0in;line-height:normal'><span style='font-size:11.5pt;
font-family:"Times New Roman",serif;color:gray;background:#EEEEEE'>   Loaded:
loaded (/lib/systemd/system/nginx.service; enabled;)</span></p>

<p class=MsoNormal style='margin-top:12.0pt;margin-right:0in;margin-bottom:
12.0pt;margin-left:0in;line-height:normal'><span style='font-size:11.5pt;
font-family:"Times New Roman",serif;color:gray;background:#EEEEEE'>   Active:</span><span
style='font-size:11.5pt;font-family:"Times New Roman",serif;color:#003366;
background:#EEEEEE'> </span><b><span style='font-size:11.5pt;font-family:"Times New Roman",serif;
color:green;background:#EEEEEE'>active (Nginx server running)</span></b></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000006
src="aws%20report_files/image007.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>Step 5:-
NGINX installed successfully.</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000007
src="aws%20report_files/image008.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-size:12.0pt;line-height:107%;font-family:
"Times New Roman",serif'>Step 6:- adding the server and proxy server in NGINX.</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000008
src="aws%20report_files/image009.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>Step 7:-
Updating the changes in the file by using reload command.</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>Sudo nano
nginx -s reload</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000009
src="aws%20report_files/image010.jpg"></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>Final
Result :-</span></p>

<p class=MsoNormal><span style='font-family:"Times New Roman",serif'>We have
added amazon website as a proxy in place of our instance’s IP address.</span></p>

<p class=MsoNormal><img border=0 width=599 height=337 id=rectole0000000010
src="aws%20report_files/image011.jpg"></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><span style='font-size:14.0pt;font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'><br>
<br>
</span></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>Applications</span></u></b></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'><br>
<br>
</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><a
href="https://www.cloudflare.com/learning/cdn/cdn-load-balance-reliability/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>Load
balancing</span></a><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black'>&nbsp;- A popular website that gets millions of users every day
may not be able to handle all of its incoming site traffic with a single origin
server. Instead, the site can be distributed among a pool of different servers,
all handling requests for the same site. In this case, a reverse proxy can
provide a load balancing solution which will distribute the incoming traffic
evenly among the different servers to prevent any single server from becoming
overloaded. In the event that a server fails completely, other servers can step
up to handle the traffic.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><b><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black'>Protection from attacks</span></b><span style='font-size:12.0pt;
font-family:"Times New Roman",serif;color:black'>&nbsp;- With a reverse proxy
in place, a web site or service never needs to reveal the IP address of their
origin server(s). This makes it much harder for attackers to leverage a
targeted attack against them, such as a&nbsp;</span><a
href="https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>DDoS
attack</span></a><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black'>. Instead the attackers will only be able to target the reverse
proxy, such as Cloudflare’s&nbsp;</span><a
href="https://www.cloudflare.com/learning/cdn/what-is-a-cdn/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>CDN</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>,
which will have tighter security and more resources to fend off a cyber attack.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><a
href="https://www.cloudflare.com/learning/cdn/glossary/global-server-load-balancing-gslb/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>Global
Server Load Balancing</span></a><b><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black'>&nbsp;(GSLB)</span></b><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>&nbsp;-
In this form of load balancing, a website can be distributed on several servers
around the globe and the reverse proxy will send clients to the server that’s
geographically closest to them. This decreases the distances that requests and
responses need to travel, minimizing load times.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><b><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black'>Caching</span></b><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black'>&nbsp;- A reverse proxy can also&nbsp;</span><a
href="https://www.cloudflare.com/learning/cdn/what-is-caching/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>cache</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>&nbsp;content,
resulting in faster performance. For example, if a user in Paris visits a
reverse-proxied website with web servers in Los Angeles, the user might
actually connect to a local reverse proxy server in Paris, which will then have
to communicate with an origin server in L.A. The proxy server can then cache
(or temporarily save) the response data. Subsequent Parisian users who browse
the site will then get the locally cached version from the Parisian reverse
proxy server, resulting in much faster performance.</span></p>

<p class=MsoNormal style='margin-top:5.0pt;margin-right:0in;margin-bottom:.25in;
margin-left:.75in;text-indent:-.25in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><b><span style='font-size:12.0pt;font-family:"Times New Roman",serif;
color:black'>SSL encryption</span></b><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black'>&nbsp;-&nbsp;</span><a
href="https://www.cloudflare.com/learning/ssl/what-is-encryption/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>Encrypting</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>&nbsp;and
decrypting&nbsp;</span><a
href="https://www.cloudflare.com/learning/security/glossary/what-is-ssl/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>SSL</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>&nbsp;(or&nbsp;</span><a
href="https://www.cloudflare.com/learning/security/glossary/transport-layer-security-tls/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>TLS</span></a><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>)
communications for each client can be computationally expensive for an origin
server. A reverse proxy can be configured to decrypt all incoming requests and
encrypt all outgoing responses, freeing up valuable resources on the origin
server.</span></p>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'>
<br>
<br>
</span></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>Conclusion</span></u></b></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal style='margin-bottom:0in;text-align:justify;text-justify:
inter-ideograph;line-height:normal'><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Thus,
we have effectively and correctly designed the Reverse proxy using NGINX. The
implementation and working, was carried out successfully and the results obtained
were as expected. Therefore, the objective of Reverse proxy has been achieved.</span></p>

<p class=MsoNormal style='margin-bottom:12.0pt;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'><br>
<br>
<br>
</span></p>

<p class=MsoNormal align=center style='margin-bottom:0in;text-align:center;
line-height:normal'><b><u><span style='font-size:14.0pt;font-family:"Times New Roman",serif;
color:black'>References</span></u></b></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'>&nbsp;</span></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><b><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:black'>Web
References:</span></b></p>

<p class=MsoNormal style='margin-bottom:0in;line-height:normal'><span
style='font-size:12.0pt;font-family:"Times New Roman",serif'><br>
<br>
</span></p>

<p class=MsoNormal style='margin-top:0in;margin-right:0in;margin-bottom:0in;
margin-left:.5in;text-indent:-.25in;line-height:normal'><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><a
href="https://www.cloudflare.com/en-in/learning/cdn/glossary/reverse-proxy/"><span
style='font-family:"Times New Roman",serif;color:blue'>https://www.cloudflare.com/en-in/learning/cdn/glossary/reverse-proxy/</span></a></p>

<p class=MsoNormal style='margin-top:0in;margin-right:0in;margin-bottom:0in;
margin-left:.5in;text-indent:-.25in;line-height:normal'><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><a
href="https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/How-to-setup-Nginx-reverse-proxy-servers-by-example"><span
style='font-family:"Times New Roman",serif;color:blue'>https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/How-to-setup-Nginx-reverse-proxy-servers-by-example</span></a></p>

<p class=MsoNormal style='margin-top:0in;margin-right:0in;margin-bottom:0in;
margin-left:.5in;text-indent:-.25in;line-height:normal'><span style='font-size:
12.0pt;font-family:"Times New Roman",serif;color:black'>•<span
style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</span></span><a href="https://www.nginx.com/blog/setting-up-nginx/"><span
style='font-size:12.0pt;font-family:"Times New Roman",serif;color:blue'>https://www.nginx.com/blog/setting-up-nginx/</span></a></p>

<p class=MsoNormal style='margin-top:0in;margin-right:0in;margin-bottom:0in;
margin-left:.5in;line-height:normal'><span style='font-size:12.0pt;font-family:
"Times New Roman",serif;color:black'>&nbsp;</span></p>

</div>

</body>

