+++
author = "Kyrill"
categories = ["Encryption", "Open Source", "Privacy", "Tech"]
date = 2015-11-13T19:07:59Z
description = ""
draft = false
image = "https://ap-content.storage.googleapis.com/2015/11/Selection_005.png"
slug = "lets-encrypt-and-http2"
tags = ["Encryption", "Open Source", "Privacy", "Tech"]
title = "Let's Encrypt and HTTP/2"

+++



## Preamble

A while ago, I signed up for [Letsencrypt](https://letsencrypt.org/) – a free, short-duration, open-source trusted CA service that runs locally on your machine and is aimed at developers and web geeks who want to secure their web apps without either paying through the nose for SSL certs issued by the likes of VeriSign or DigiCert.

As a side note, [DigiCert](https://www.digicert.com) are amazing and if you are an enterprise user who needs an incredibly fast, flexible and reasonably-priced certificate provider, you should definitely look into using them. Their turnaround times on new certs are less than 5 minutes, they support unlimited duplicate certificates for wildcard certs, in case you need to secure a single service that doesn’t support wildcards (Oracle Wallet, PulseSecure MAG gateways, etc, etc) and their support line is great.

Anyhow, the beta programme for letsencrypt can be found here: https://docs.google.com/forms/d/15Ucm4A20y2rf9gySCTXD6yoLG6Tba7AwYgglV7CKHmM

Once you get your application approved – it takes 3-4 days, depending on who’s keeping an eye on the applications queue – you can start generating SSL certificates for the domain you applied for. They don’t support wildcard or subdomain certificates in the beta yet, but they plan to do this once they go live.

Once you grab the code for the letsencrypt client from GitHub, you can start generating certificates in minutes.


## Generate your certs

Letsencrypt supports autoconfiguration for Apache and very limited and unstable autoconfig for nginx, but to be absolutely sure of what’s happening, I went with the bare-bones certificate-only option that will generate the key, CSR and get you a cert in about 30 seconds. You can then do what you want with your certificate.

Getting it running is laughably simple:

`./letsencrypt-auto certonly -d yoursite.com --server https://acme-v01.api.letsencrypt.org/directory --agree-dev-preview`

This will spit out the certificate + CA bundle in /etc/letsencrypt/live/ which you can then use from wherever you want. If you prefer, you can move the certs somewhere else too.


## nginx

If you want to just use plain old SSL/TLS, you can stick with the current version of nginx that is available in all popular package repositories. If you want to play with the *fun* toys, though, you’ll need the latest and greatest stable version – at time of writing that is *1.9.6*. On an Ubuntu or Debian box, you can [follow the instructions here](https://www.nginx.com/blog/nginx-1-9-5/) to get it set up.

Once you’re up and running, getting your SSL site running is a breeze. All you need is to set up a new vHost to run your SSL content from. Usually, this will reside in `/etc/nginx/sites-available/yourvhost` or `/etc/nginx/conf.d/yourvhost.conf`. *By default, nginx 1.9.6 uses the latter pattern in favour of the sites-available/sites-enabled pattern*

Below is an example.

server { listen 443 ssl http2; server_name yoursite.com;   root /var/www/yoursite.com;   index index.php; access_log /var/log/nginx/yoursite.com.ssl.access.log;   error_log /var/log/nginx/yoursite.com.ssl.error.log;   ssl on;   ssl_certificate /etc/letsencrypt/live/yoursite.com/fullchain.pem;   ssl_certificate_key /etc/letsencrypt/live/yoursite.com/privkey.pem; ssl_protocols TLSv1.1 TLSv1.2;   ssl_prefer_server_ciphers off;   ssl_stapling on;   ssl_stapling_verify on;   ssl_session_timeout 10m;   ssl_session_cache shared:ssl_session_cache:10m;   resolver 8.8.8.8 8.8.4.4 valid=300s;   resolver_timeout 5s; add_header Alternate-Protocol 443:npn-spdy/3; add_header Strict-Transport-Security max-age=63072000; add_header X-Frame-Options DENY; add_header X-Content-Type-Options nosniff; location / { ... } }

If you used *sites-available*, you will need to symlink your new vhost into *sites-enabled* – `ln -s /etc/nginx/sites-available/yoursite /etc/nginx/sites-enabled/yoursite`

Quickly run `nginx -t` to make sure your config checks out and `nginx -s reload` to reload the config.

Once that’s done, you should be up and running and able to access your site over HTTPS!

 

Happy encrypting!


