---
title: Samsung M2020 on xUbuntu
author: Iulian
type: post
date: 2015-12-11T20:19:58+00:00
url: /2015/12/samsung-m2020-on-xubuntu/
categories:
  - Setup environment

---
<p style="text-align: justify;">
  Open gnome-terminal via xUbuntu dash or via shortcut ctrl+alt+t and paste following commands:
</p>

  1. Download driver from Samsung site:

<pre class="lang:batch decode:true">sudo wget http://org.downloadcenter.samsung.com/downloadfile/ContentsFile.aspx?CDSite=UNI_LEVANT&CttFileID=5999976&CDCttType=DR&ModelType=N&ModelName=SL-M2020&VPath=DR/201503/20150311160833703/ULD_v1.00.35.tar.gz</pre>

2. Unpack the driver:

<pre class="lang:batch decode:true">tar zxvf ULD_v1.00.35.tar.gz</pre>

<p style="text-align: justify;">
  3. Install driver, with sudo command you need to enter your root password:
</p>

<pre class="lang:batch decode:true">sudo ./uld/install.sh</pre>

<p style="text-align: justify;">
  4. Now, you should be able to to add your printer via Printers program. Add it as network printer or simply plug it in via USB and it should work.
</p>

&nbsp;

&nbsp;