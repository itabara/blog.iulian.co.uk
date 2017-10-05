---
title: Setup SSH Keys for remote access
author: Iulian
type: post
date: 2014-02-22T11:28:16+00:00
url: /2014/02/setup-ssh-keys-for-remote-access/
categories:
  - DevOps
tags:
  - ssh

---
<p style="text-align: justify;">
  SSH keys are a more secured way to connect to your servers / VPS, compared to simple password authentication. Once you&#8217;ve setup a SSH key pair they can be deployed on all your servers in order to allow secured access. To add an additional layer of protection you can also password protect your SSH keys.
</p>

<h2 style="text-align: justify;">
  Creating SSH keys
</h2>

<p style="text-align: justify;">
  There are few options and tools to generate SSH keys, but for windows based systems the <a href="http://www.chiark.greenend.org.uk/%7Esgtatham/putty/download.html" target="_blank">PuTTYgen</a> is the way to go.
</p>

<p style="text-align: justify;">
  <a href="http://www.iuliantabara.com/2014/01/setup-ssh-keys-for-remote-access/putty-key-generator/" rel="attachment wp-att-981"><img class="aligncenter size-full wp-image-981" src="http://www.iuliantabara.com/wp-content/uploads/2016/01/putty-key-generator.png" alt="putty-key-generator" width="479" height="466" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/01/putty-key-generator.png 479w, https://www.iuliantabara.com/wp-content/uploads/2016/01/putty-key-generator-300x292.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/01/putty-key-generator-308x300.png 308w" sizes="(max-width: 479px) 100vw, 479px" /></a>
</p>

<p style="text-align: justify;">
  I use <strong>SSH-2 RSA</strong> and <strong>2048 bits</strong> for generated key (increasing the bits makes it harder to crack the key by brute-force methods).
</p>

<p style="text-align: justify;">
  After generating the keys you should store them in a safe place (specially the private one). If you lose your keys and have disabled username/password logins, you will no longer be able log in!)
</p>

<p style="text-align: justify;">
  <strong>NOTE:</strong> PuTTY and OpenSSH use different formats for public SSH keys. If the <strong>SSH Key</strong> you copied starts with “—- BEGIN SSH2 PUBLIC KEY …”, it is in the wrong format. Be sure to follow the instructions carefully. Your key should start with “ssh-rsa AAAA ….”
</p>

<h2 style="text-align: justify;">
  Deploy the Public Key on your Server
</h2>

<p style="text-align: justify;">
  You need to upload the public key in the file <strong>~/.ssh/authorized_keys</strong> on your server.
</p>

1. Log in to your destination server using <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">puTTY</a>.

2. If your SSH folder does not yet exist, create it manually:

<pre class="lang:sh decode:true">mkdir ~/.ssh
chmod 0700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 0644 ~/.ssh/authorized_keys</pre>

3. Paste/insert the content of the public key in **autorized_keys** file.

## Create the Putty Profile

<p style="text-align: justify;">
  1. Open putty.exe and specify the hostname (FQD name or IP), select connection type to SSH.
</p>

<p style="text-align: justify;">
  2. Navigate to Connection -> Data -> Login details -> Autologin username -> specify the username for the account you want to login.
</p>

<p style="text-align: justify;">
  3.Navigate to Connection -> SSH -> Auth -> Browse -> private key file saved from PuttyGen.
</p>

<a href="http://www.iuliantabara.com/2014/01/setup-ssh-keys-for-remote-access/putty-settings/" rel="attachment wp-att-982"><img class="aligncenter size-full wp-image-982" src="http://www.iuliantabara.com/wp-content/uploads/2016/01/putty-settings.png" alt="putty-settings" width="453" height="438" srcset="https://www.iuliantabara.com/wp-content/uploads/2016/01/putty-settings.png 453w, https://www.iuliantabara.com/wp-content/uploads/2016/01/putty-settings-300x290.png 300w, https://www.iuliantabara.com/wp-content/uploads/2016/01/putty-settings-310x300.png 310w" sizes="(max-width: 453px) 100vw, 453px" /></a>

4. Go to Session and hit Save button to keep the settings.

5. Enjoy

## Disable Password Login

You can go further and add the extra security that SSH keys offer by disabling password login to your server. Before you do this it is essential you keep your SSH key files in a safe place and take a backup… in another safe place.

**When password login is disabled you won’t be able to login without these keys**.

<p style="text-align: justify;">
  On debian/Ubuntu systems the SSH password authentication can be disabled by editing <strong><span class="crayon-o">/</span><span class="crayon-v">etc</span><span class="crayon-o">/</span><span class="crayon-v">ssh</span><span class="crayon-o">/</span></strong><span class="crayon-v"><strong>sshd_config</strong>.</span>
</p>

<pre class="lang:sh decode:true ">[...]
PasswordAuthentication no
[...]
UsePAM no
[...]</pre>

Please don&#8217;t forget to restart your SSH daemon service:

<pre class="lang:sh decode:true ">service ssh restart</pre>

Now your servers should be secured with SSH keys.

If you&#8217;re on linux as a client, things are much easier:

<pre class="lang:sh decode:true ">ssh-keygen -t rsa</pre>

The public key can now be traced to the link ~/.ssh/id_rsa.pub

Now it&#8217;s time to place the public key on the server that we intend to use:

<pre class="lang:sh decode:true">ssh-copy-id user@xxx.xxx.xxx.xxx</pre>

<p style="text-align: justify;">
  While a password stands the risk of being finally cracked, SSH keys are rather impossible to decipher using brute force.
</p>