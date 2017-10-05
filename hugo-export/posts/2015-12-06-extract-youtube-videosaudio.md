---
title: Extract Youtube videos/audio
author: Iulian
type: post
date: 2015-12-06T10:37:06+00:00
url: /2015/12/extract-youtube-videosaudio/
categories:
  - Linux

---
<p style="text-align: justify;">
  One of my favorite Linux applications is the command-line utility <a title="youtube-dl" href="https://rg3.github.io/youtube-dl/" target="_blank">youtube-dl</a>, which I use quite often to download YouTube videos or extract only the audio.
</p>

<p style="text-align: justify;">
  So, let&#8217;s install.
</p>

<pre class="lang:batch decode:true">apg-get install youtube-dl</pre>

Extract the mp3 file from youtube url:

<pre class="lang:batch decode:true">youtube-dl --extract-audio --audio-format mp3 -t &lt;youtube_link&gt;</pre>

Extract the mp3 file from youtube url at 128k audio quality:

<pre class="lang:batch decode:true">youtube-dl --extract-audio --audio-format mp3 --audio-quality 128K -t &lt;youtube_link&gt;</pre>

Extract the mp3 file from youtube url at the best audio quality:

<pre class="lang:batch decode:true">youtube-dl --extract-audio --audio-format best -t &lt;youtube_link&gt;</pre>

Display available video formats:

<pre class="lang:batch decode:true">youtube-dl -F &lt;youtube_link&gt;</pre>

Extract the video:

<pre class="lang:batch decode:true ">youtube-dl -f &lt;format_id&gt; -o &lt;youtube_link&gt;</pre>