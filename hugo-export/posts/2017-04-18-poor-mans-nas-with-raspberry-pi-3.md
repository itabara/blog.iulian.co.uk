---
title: Poor man’s NAS with Raspberry PI 3
author: Iulian
type: post
date: 2017-04-18T15:19:51+00:00
url: /2017/04/poor-mans-nas-with-raspberry-pi-3/
categories:
  - Raspberry_PI

---
<p style="text-align: justify;">
  <strong>Disclaimer:</strong> Raspberry PI is using the Micro SD Card to keep the OS files (I&#8217;m using <a href="https://www.raspberrypi.org/downloads/raspbian/">Raspbian Jessie</a>) and unfortunately the card storage solution isn&#8217;t 100% reliable because of card data corruption. It&#8217;s happening rarely, but is happening. Details how to minimize the risks <a href="https://raspberrypi.stackexchange.com/questions/43212/how-likely-is-sd-card-corruption">here</a>. All my important files are stored on an external 3.5 HDD with own power source.
</p>

<p style="text-align: justify;">
  <strong>The goal:</strong>
</p>

<li style="text-align: justify;">
  Easy to access external 2TB drive from intranet via samba
</li>
<li style="text-align: justify;">
  Easy to access my external 2TB drive from internet via owncloud (alternative: scp)
</li>
<li style="text-align: justify;">
  Run the nginx web server secured, pointing to custom domain (eg. https://www.emilian.co.uk) and reverse proxy to few custom urls (details below)
</li>
<li style="text-align: justify;">
  Run plex server (https://plex.emilian.co.uk)
</li>
<li style="text-align: justify;">
  Run subsonic server (https://music.emilian.co.uk)
</li>
<li style="text-align: justify;">
  Run deluge UI (https://deluge.emilian.co.uk) with custom authentication
</li>
<li style="text-align: justify;">
  Dynamic DNS client (<a href="http://freedns.afraid.org">http://freedns.afraid.org)</a>
</li>

## Initial steps

<li style="text-align: justify;">
  Download <a href="https://downloads.raspberrypi.org/raspbian_lite_latest">Raspbian Lite</a> and unzip to your home directory
</li>
<li style="text-align: justify;">
  Insert a new Micro SD Card into your computer and write the Raspbian image to your card. I&#8217;m using OSX so the quick steps are:
</li>

<pre class="lang:sh decode:true">sudo diskutil list -&gt; identity your disk#
sudo dd bs=1m if=2017-04-10-raspbian-jessie-lite.img of=/dev/rdisk2
sudo touch /Volumes/boot/ssh -&gt; enable ssh on your raspberry pi</pre>

<p style="text-align: justify;">
  We&#8217;re ready to unmount the card from your computer and install it in the raspberry pi. Please make sure you connected the network (I prefer wired but wireless is available too) and your Raspberry Pi is powered by min 2.5A (I&#8217;m using a cell phone quick charger and that could explain why I&#8217;m having card corruption issues). You can find a genuine power supply <a href="https://thepihut.com/collections/raspberry-pi-power-supplies/products/official-raspberry-pi-universal-power-supply">here</a>.
</p>

<p style="text-align: justify;">
  We&#8217;re ready to ssh on our raspberry pi. I&#8217;m using a static IP based on MAC address so it&#8217;s easy for me to ssh on that IP. There are plenty of tools to scan your network and find the ip of your raspberry pi (a quick one is to use <strong>nmap -v -sn 192.168.0.0/24</strong>). My IP is 192.168.0.112.
</p>

<p style="text-align: justify;">
  I&#8217;m connecting to my raspberry pi and first I change the default password and create a new user. Then I run raspi-conf to customize the current installation (eg. timezone, setting hostname, set locale (<code>en_US.UTF-8</code>),  <a href="http://elinux.org/RPi_Resize_Flash_Partitions">expand the boot partition</a>)
</p>

<pre class="lang:sh decode:true">ssh pi@192.168.0.112 (default password: raspberry)
passwd -&gt; change pi password
sudo adduser iulian -&gt; confirm the details
sudo raspi-config</pre>

I&#8217;m adding my user to sudo group

<pre class="lang:sh decode:true">sudo usermod -aG sudo iulian</pre>

## Fix mirror for apt-get (Optional)

If apt mirror is causing issues when doing the update the quick workaround I&#8217;m applying is:

1.Open `/etc/hosts` file with sudo rights `$sudo nano /etc/hosts`

2.Paste the following lines at the end of `/etc/hosts` file.

<pre class="lang:sh decode:true">93.93.128.193   mirrordirector.raspbian.org
93.93.128.191   archive.raspbian.org</pre>

&nbsp;

The next step is to ssh with my new user, update my apt repos and start installing packages.

<pre class="lang:sh decode:true">ssh iulian@192.168.0.112
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install vim</pre>

## Updating Firmware (Optional)

<p style="text-align: justify;">
  Your Raspbian installation also included a pretty recent copy of the Raspberry Pi firmware. However, sometimes there are important updates, so I recommend to use the latest firmare version:
</p>

<pre class="lang:sh decode:true">sudo apt-get install rpi-update -y
sudo rpi-update
sudo reboot</pre>

## Mount external drive

<p style="text-align: justify;">
  In order to achieve the goal #1 we&#8217;ll need to opt for an external HDD drive (I&#8217;m using a 2 TB drive, external power supply) formatted as ext4. You can find your external drive by running below command:
</p>

<pre class="lang:sh decode:true">sudo lsblk -f 
#sudo fdisk -l</pre>

<pre class="">sudo mkdir /nas</pre>

<p style="text-align: justify;">
  Add below line into /etc/fstab file to mount your external drive automatically:
</p>

<pre class="lang:sh decode:true">proc            /proc           proc    defaults          0       0
/dev/mmcblk0p1  /boot           vfat    defaults          0       2
/dev/mmcblk0p2  /               ext4    defaults,noatime  0       1
#/dev/sda1	/nas	ntfs-3g	rw,default	0	0
<strong>/dev/sda1	/nas	ext4	defaults	0	0</strong>
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that
</pre>

<p style="text-align: justify;">
  Mount entry in the “/etc/fstab” file shall make this mount permanent across reboot. Reboot and to check if the external disk is mounted in the <strong>/nas</strong> location.
</p>

<pre class="lang:sh decode:true ">sudo reboot
sudo lsblk -f -o NAME,LABEL,SIZE,FSTYPE,MOUNTPOINT
df
sudo mount -a</pre>

<p style="text-align: justify;">
  Now we have assigned a fixed mount point for the external disk drive, which is available under “/nas” directory in our raspberry pi device.
</p>

<p style="text-align: justify;">
  In case our external connected usb disk takes a long time to initiate this automount might fail and you may need to initiate the system mount at a later time. Adding an entry to <strong>/etc/<span class="skimlinks-unlinked">rc.local</span> </strong>can do the trick. Add below lines before <strong>exit</strong> line:
</p>

<pre class="lang:sh decode:true">sleep 30
sudo mount -a
exit</pre>

## Install and configure samba

<pre class="lang:sh decode:true">sudo apt-get install samba -y
sudo apt-get install samba-common-bin -y</pre>

We&#8217;ll use the folder “/nas” where our 2TB external drive is mounted. We&#8217;ll share “/nas” using samba. To do this, we need a samba account in our raspberry Pi. We&#8217;ll use the user account “iulian” in this case. Enter the following command and type in a password to set the samba password for “iulian” user:

<pre class="lang:sh decode:true">sudo smbpasswd -a iulian</pre>

The password you set for your samba share will not affect your login to Raspberry Pi over ssh.

Let&#8217;s backup the original smb.config file.

<pre class="lang:sh decode:true">sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.orig
sudo vim /etc/samba/smb.conf</pre>

<div>
  Add the following section in the <strong><span class="skimlinks-unlinked">smb.conf</span></strong> file:
</div>

<div>
  <pre class="lang:sh decode:true ">security = user

// your shared definition

[nas]
comment = Nas share
path = /nas
browseable = yes
writeable = yes
only guest = no
create mask = 0777
directory mask = 0777
public = no
read only = no
force user = root</pre>
  
  <p>
    Restart samba service:
  </p>
  
  <pre class="lang:sh decode:true">sudo /etc/init.d/samba restart</pre>
  
  <p style="text-align: justify;">
    Check if samba service is enabled on startup, so that the service starts on reboot (usually it is)
  </p>
  
  <pre class="lang:sh decode:true">sudo service --status-all | grep  samba

pi@nas:~ $ sudo service --status-all | grep  samba
 [ + ]  samba
 [ + ]  samba-ad-dc</pre>
  
  <p>
    The “[ + ]” indicates the service is active on startup.
  </p>
  
  <p style="text-align: justify;">
    You can mount your /nas folder to your local computer and check if the share works as you expect.
  </p>
  
  <h2>
    Installing nginx and configure reverse proxy
  </h2>
  
  <p style="text-align: justify;">
    Nginx is a light and modern web server and fits perfectly our raspberry pi configuration.
  </p>
  
  <pre class="lang:sh decode:true">sudo apt-get install nginx
sudo /etc/init.d/nginx start</pre>
  
  <p style="text-align: justify;">
    You should be able to see nginx is up and running by opening your local browser with <strong>http://192.168.0.112</strong> (raspberry pi assigned ip).
  </p>
  
  <h2 style="text-align: justify;">
    Install PHP 7 (optional)
  </h2>
  
  <p style="text-align: justify;">
    Raspbian being based on Debian Jessie ships with PHP 5.6 by default. But we want to run the latest PHP version. To install PHP 7 on Raspbian we must switch to the <strong>testing branch</strong> of Raspbian, commonly known by the codename <strong>stretch.</strong>
  </p>
  
  <pre class="lang:sh decode:true ">sudo vim /etc/apt/sources.list</pre>
  
  <p>
    Add below line at the bottom of the file:
  </p>
  
  <pre class="lang:sh decode:true ">deb http://mirrordirector.raspbian.org/raspbian/ stretch main contrib non-free rpi</pre>
  
  <p style="text-align: justify;">
    By adding this all installs or updates will default to use the newer versions of files available in the <strong>stretch</strong> release which is not considered 100% stable. To prevent that, we&#8217;ll pin all packages to use the <strong>jessie</strong> release with a higher priority by default. To do this create a preferences file:
  </p>
  
  <pre class="lang:sh decode:true ">sudo vim /etc/apt/preferences</pre>
  
  <pre class="lang:sh decode:true ">Package: *
Pin: release n=jessie
Pin-Priority: 600</pre>
  
  <pre class="lang:sh decode:true">sudo apt-get update -y
sudo apt-get install -t stretch php7.0 php7.0-curl php7.0-gd php7.0-fpm php7.0-cli php7.0-opcache php7.0-mbstring php7.0-xml php7.0-zip
php -v</pre>
  
  <p>
    Now we&#8217;ll need to configure nginx in order to support our needs.
  </p>
  
  <pre class="lang:sh decode:true ">sudo vim /etc/nginx/conf.d/proxy-control.config</pre>
  
  <pre class="lang:sh decode:true ">proxy_connect_timeout   59s;
proxy_send_timeout      600;
proxy_read_timeout      36000s;  ## Timeout after 10 hours
proxy_buffer_size       64k;
proxy_buffers           16 32k;
proxy_pass_header       Set-Cookie;
proxy_hide_header       Vary;

proxy_busy_buffers_size         64k;
proxy_temp_file_write_size      64k;

proxy_set_header        Accept-Encoding         '';
proxy_ignore_headers    Cache-Control           Expires;
proxy_set_header        Referer                 $http_referer;
proxy_set_header        Host                    $host;
proxy_set_header        Cookie                  $http_cookie;
proxy_set_header        X-Real-IP               $remote_addr;
proxy_set_header        X-Forwarded-Host        $host;
proxy_set_header        X-Forwarded-Server      $host;
proxy_set_header        X-Forwarded-For         $proxy_add_x_forwarded_for;
proxy_set_header        X-Forwarded-Port        '443';
proxy_set_header        X-Forwarded-Ssl         on;
proxy_set_header        X-Forwarded-Proto       https;
proxy_set_header        Authorization           '';

proxy_buffering         off;
proxy_redirect          off;

## Required for Plex WebSockets
proxy_http_version      1.1;
proxy_set_header        Upgrade         $http_upgrade;
proxy_set_header Connection "upgrade";</pre>
  
  <pre class="lang:sh decode:true">sudo vim /etc/nginx/nginx.config</pre>
  
  <pre class="lang:sh decode:true ">user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;
	gzip_disable "msie6";

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
</pre>
  
  <pre class="lang:sh decode:true ">sudo vim /etc/nginx/sites-available/default</pre>
  
  <pre class="lang:sh decode:true ">upstream php-handler {
    server 127.0.0.1:9000;
    #server unix:/var/run/php5-fpm.sock;
}

server {
    listen 80;
    server_name emilian.co.uk;
    return 301 https://$server_name$request_uri;  # enforce https
}

server {
    listen 443 ssl;
    server_name emilian.co.uk;
    ssl_certificate /home/iulian/emilian.co.uk.ssl/emilian.co.uk.cert;
    ssl_certificate_key /home/iulian/emilian.co.uk.ssl/emilian.co.uk.key;
    
    # Add headers to serve security related headers
    # Before enabling Strict-Transport-Security headers please read into this topic first.
    add_header Strict-Transport-Security "max-age=15552000; includeSubDomains";
    #add_header X-Content-Type-Options nosniff;
    #add_header X-Frame-Options "SAMEORIGIN";
    #add_header X-XSS-Protection "1; mode=block";
    #add_header X-Robots-Tag none;
    #add_header X-Download-Options noopen;
    #add_header X-Permitted-Cross-Domain-Policies none;

    # Path to the root of your installation
    root /var/www/owncloud;
    client_max_body_size 1000M; # set max upload size
    fastcgi_buffers 64 4K;
    #fastcgi_param modHeadersAvailable true;
    rewrite ^/caldav(.*)$ /remote.php/caldav$1 redirect;
    rewrite ^/carddav(.*)$ /remote.php/carddav$1 redirect;
    rewrite ^/webdav(.*)$ /remote.php/webdav$1 redirect;
    index index.php;
    error_page 403 /core/templates/403.php;
    error_page 404 /core/templates/404.php;
    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }
    location ~ ^/(?:\.htaccess|data|config|db_structure\.xml|README) {
        deny all;
    }
    location / {
        # The following 2 rules are only needed with webfinger
        rewrite ^/.well-known/host-meta /public.php?service=host-meta last;
        rewrite ^/.well-known/host-meta.json /public.php?service=host-meta-json last;
        rewrite ^/.well-known/carddav /remote.php/carddav/ redirect;
        rewrite ^/.well-known/caldav /remote.php/caldav/ redirect;
        rewrite ^(/core/doc/[^\/]+/)$ $1/index.html;
        try_files $uri $uri/ index.php;
    }
    location ~ \.php(?:$|/) {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param HTTPS on;
        fastcgi_pass php-handler;
   }
   # Optional: set long EXPIRES header on static assets
   location ~* \.(?:jpg|jpeg|gif|bmp|ico|png|css|js|swf)$ {
        expires 30d;
        # Optional: Don't log access to assets
        access_log off;
   }
}</pre>
  
  <p>
    &nbsp;
  </p>
</div>