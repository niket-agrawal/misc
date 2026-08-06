# Setting up and Configuring nginx server, 

This section covers the installation of NGINX, basic service management, and setting up a server block (virtual host) for a website.

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
admin1@vmd200007:~$ sudo systemctl reload nginx
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









---

## In the END: check Security Headers
Use principle of least privilege
[https://securityheaders.com/?q=https%3A%2F%2Fbeehive.quest%2F&followRedirects=on](https://securityheaders.com/?q=https%3A%2F%2Fbeehive.quest%2F&followRedirects=on)












