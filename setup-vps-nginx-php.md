# Setting up and Configuring nginx server, 

This section covers the installation of NGINX, basic service management, and setting up a server block (virtual host) for a website. Also integrate PHP with server.

---

## Install NGINX

```bash
admin1@vmd200007:~$ sudo apt install nginx
admin1@vmd200007:~$ sudo systemctl status nginx      # verify service status
```

**Now, configure DNS (A Records)**, to make your website accessible via a domain name, configure A records in your DNS provider's control panel:
```bash
admin1@vmd200007:~$ curl -4 icanhazip.com            # check current ip of server
# Update your DNS A record to point example.com → 192.168.1.1
admin1@vmd200007:~$ curl -I http://localhost         # verify if nginx serves content locally
```

**Use `systemctl` to control NGINX service**,
| Command | Purpose |
| --- | --- |
| `sudo systemctl start nginx` | Start the NGINX service |
| `sudo systemctl stop nginx` | Stop the NGINX service |
| `sudo systemctl restart nginx` | Restart the NGINX service |
| `sudo systemctl reload nginx` | Reload configuration without downtime |
| `sudo systemctl enable nginx` | Enable NGINX to start on boot |
| `sudo systemctl disable nginx` | Disable auto-start on boot |

> 💡 Use `reload` when updating configuration files to apply changes without interrupting running connections.
 
---

## Setup server blocks

**Create a directory structure** for your website,
```bash
# create web root and set permissions
admin1@vmd200007:~$ sudo mkdir -p /var/www/subdomain.domain.tld/html/
admin1@vmd200007:~$ sudo chown -R $USER:$USER /var/www/subdomain.domain.tld/html/
admin1@vmd200007:~$ sudo chmod -R 755 /var/www/subdomain.domain.tld/

# Create homepage and server block configuration
admin1@vmd200007:~$ sudo nano /var/www/subdomain.domain.tld/html/index.html
admin1@vmd200007:~$ sudo nano /etc/nginx/sites-available/homepage-config-filename.conf    # see next section what to write here

# Enable site by creating softlink and reload nginx
admin1@vmd200007:~$ sudo ln -s /etc/nginx/sites-available/homepage.conf /etc/nginx/sites-enabled/
admin1@vmd200007:~$ sudo nginx -t
admin1@vmd200007:~$ sudo systemctl reload nginx			# sudo nginx -t && sudo systemctl reload nginx
```

**NGINX Config files and logs**

| Path | Purpose |
| --- | --- |
| `/etc/nginx` | The Nginx configuration directory. All of the Nginx configuration files reside here. |
| `/etc/nginx/nginx.conf` | The main Nginx configuration file. This can be modified to make changes to the Nginx global configuration. |
| `/etc/nginx/sites-available/` | The directory where per-site server blocks can be stored. Nginx will not use the configuration files found in this directory unless they are linked to the sites-enabled directory. Typically, all server block configuration is done in this directory, and then enabled by linking to the other directory. |
| `/etc/nginx/sites-enabled/` | The directory where enabled per-site server blocks are stored. Typically, these are created by linking to configuration files found in the sites-available directory. |
| `/etc/nginx/snippets` | This directory contains configuration fragments that can be included elsewhere in the Nginx configuration. Potentially repeatable configuration segments are good candidates for refactoring into snippets. |
| `/var/log/nginx/access.log` | Every request to your web server is recorded in this log file unless Nginx is configured to do otherwise. |
| `/var/log/nginx/error.log` | Any Nginx errors will be recorded in this log. |

**Simplest nginx config**
```
server {
	listen 80;
	listen [::]:80;

	server_name experiment.beehive.quest www.experiment.beehive.quest;

	root /var/www/beehive.quest/html;
	index index.html index.htm index.php;

	location / {
		try_files $uri $uri/ =404;}
}
```


---

## Install SSL certificate (Let's Encrypt)
```bash
admin1@vmd200007:~$ sudo apt install certbot python3-certbot-nginx
admin1@vmd200007:~$ sudo certbot --nginx -d beehive.quest -d www.beehive.quest            # issues the certificates and saves them
admin1@vmd200007:~$ sudo nginx -T | grep ssl_               # verify SSL Configuration
admin1@vmd200007:~$ sudo systemctl status certbot.timer     # Check auto-renewal timer
admin1@vmd200007:~$ sudo certbot renew --dry-run            # Test renewal without applying

# https://www.youtube.com/watch?v=_jKXoS8uZug --- how to add more sites for certificates
# For, WILDCARD CERTBOT
# ⚠️ Requires manual DNS TXT record verification
admin1@vmd200007:~$ sudo certbot certonly --manual --preferred-challenges=dns -d "*.beehive.quest" -d beehive.quest
```

---

## PHP 8.4 with Nginx
```bash
admin1@vmd200007:~$ sudo apt install php8.4 php8.4-fpm
admin1@vmd200007:~$ php --version							# verify installation
admin1@vmd200007:~$ which php
admin1@vmd200007:~$ ls /var/run/php							# confirm that socket exists

# Create test file,  <?php phpinfo(); ?>
admin1@vmd200007:~$ nano /var/www/homepage/html/tests/testphp.php

# Restart PHP-FPM and reload Nginx, visit the webpage to see if it works
admin1@vmd200007:~$ sudo systemctl restart php8.4-fpm
admin1@vmd200007:~$ sudo nginx -t && sudo systemctl reload nginx

# sqlite functionality
admin1@vmd200007:~$ apt install php8.4-sqlite3
admin1@vmd200007:~$ systemctl restart php8.4-fpm
admin1@vmd200007:/var/log$ php8.4 -m | grep -i sqlite		# verify
```
> ⚠️ Remove testphp.php after testing — never leave phpinfo() exposed in production

**Key paths**
| Path | Purpose |
| --- | --- |
| `/etc/php/8.4/fpm/pool.d/*` | PHP-FPM pool config files |
| `/var/run/php/*.sock` | PHP-FPM sockets |
| `/var/log/php8.4-fpm.log` | PHP-FPM logs |

**Secure nginx config, with PHP**
```
server {
    server_name beehive.quest www.beehive.quest;

	root /var/www/beehive.quest/homepage/html;
    index index.html index.htm index.nginx-debian.html index.php;

 	# Add security headers here, see last section   

	location / {
		try_files $uri $uri/ =404;}

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;}

    # SSL Configuration (managed by Certbot) - automatically added
    listen [::]:443 ssl ipv6only=on;
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/beehive.quest/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/beehive.quest/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

# HTTP to HTTPS (also automatically managed by Certbot) - automatically added
server {
	if ($host = www.beehive.quest) {
		return 301 https://$host$request_uri;}
    if ($host = beehive.quest) {
		return 301 https://$host$request_uri;}
    listen 80;
    listen [::]:80;
    server_name beehive.quest www.beehive.quest;
    return 404;
}
```
> ⚠️ Note: check, certbot might not add automatically if you use wildcard type, it would be more manual.




---

## In the END: check Security Headers
Use principle of least privilege
[https://securityheaders.com/?q=https%3A%2F%2Fbeehive.quest%2F&followRedirects=on](https://securityheaders.com/?q=https%3A%2F%2Fbeehive.quest%2F&followRedirects=on)

```
    # Security Headers
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Permissions-Policy "camera=(), microphone=(), geolocation=(), accelerometer=(), gyroscope=(), clipboard-read=(), display-capture=(), payment=(), usb=()" always;
    add_header Strict-Transport-Security "max-age=300" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
	add_header Content-Security-Policy "default-src 'self'; img-src 'self' https: data:; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'" always;
    server_tokens off;
```

**🛡️ Security Features Explained:**
| Header | Purpose |
| --- | --- |
| Referrer-Policy | Limits referrer information sent to external sites |
| Permissions-Policy | Disables sensitive device access (camera, mic, geolocation, etc.) |
| Strict-Transport-Security | Enforces HTTPS (max-age=300s = 5 minutes) |
| X-Frame-Options | Prevents clickjacking by blocking embedding in frames |
| X-XSS-Protection | Enables XSS filtering in older browsers |
| X-Content-Type-Options | Prevents MIME-sniffing attacks |
| server_tokens off | Hides NGINX version (security through obscurity) |
| Content-Security-Policy | Restricts sources for scripts, images, styles |

> ⚠️ Note: CSP is set to allow 'unsafe-inline' for scripts and styles — only use in development or if you control all content. For production, prefer nonce or hash-based policies.
