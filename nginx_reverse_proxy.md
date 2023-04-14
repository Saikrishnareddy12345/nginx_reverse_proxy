**Setup HostBased Application for Rewardx console for Dev environment.**


**Step 1**: 
	Open CloudFlare by using https://dash.cloudflare.com/
	By Default you are moved to the Websites page.
	Select and enter on getwalkin.in Domain.
	
	
**Step 2**:
	Go to the Left Menu Click on DNS and Create a Record that should be only A Record.
	Only.Give the SubDomain Name(dev) and put it here IP Address(which is from DigitalOcean).
	Save Record.
	
	
**Step 3**: 
	Open terminal in your Local and Do ssh to the rewardx-console server.
	Install Nginx Reverse Proxy. The Proxy setup is as follows.
	
	
**First Install Certbot On server**:
**Certbot Installation**:-
----------------
	Step 1 — Installing Certbot
	
	```
	Cmd: sudo snap install core; sudo snap refresh core
	```

	Remove if already installed
	
	```
	Cmd: sudo apt remove certbot

	sudo snap install --classic certbot
	
	```
	
	```
	Cmd: sudo ln -s /snap/bin/certbot /usr/bin/certbot
	```

	- Obtain certificate
	
	```
	sudo certbot certonly --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory --manual-public-ip-logging-ok -ddev.saikrishnareddy.xyz
	```

	Step 3 — Allowing HTTPS Through the Firewall
	
	```
	Cmd: sudo ufw status
	```
	
	Step 4 — If it is not allowed ,do enable
	
	```
	Cmd: sudo ufw enable && sudo ufw allow 'Nginx HTTP' && sudo ufw allow ‘Nginx Full’
	```
	
	Check the ufw List: ```
	sudo ufw app list
	```

	Step 5 — Verifying Certbot Auto-Renewal
	
	```
	Cmd: sudo systemctl status snap.certbot.renew.service
	Cmd: sudo certbot renew --dry-run
	```

***Step 4***:
	Install Nginx Reverse Proxy:
	
	Step 1 — Update the server First.
	
	Cmd: sudo apt update -y
	
	Step 2 — Install Nginx service
	
	Cmd: apt install nginx -y
	
	Step 3 — start the Nginx Service
	
	Cmd: systemctl start nginx && systemctl enable nginx && systemctl status nginx
	
	Step 4 — Go to the Nginx proxy Path and edit the config file.
	
	Cmd: cd /etc/nginx/sites-available/
	
	Edit the default file and write the following code
	
**vi default**

—--------------------------------------------------

```
server {
    server_name dev.saikrishnareddy.xyz;

    access_log  /var/log/nginx/dev-access.log;
    error_log  /var/log/nginx/dev-error.log;

    location / {
        proxy_pass         http://127.0.0.1:3000;
        proxy_redirect     off;

        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_connect_timeout 60;
        proxy_read_timeout 60;
    }


    listen 443 ssl; # managed by Certbot
    #listen  ssl;
    ssl_certificate /etc/letsencrypt/live/dev.saikrishnareddy.xyz/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/dev.saikrishnareddy.xyz/privkey.pem; # managed by Certbot
    #include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    #ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = dev.saikrishnareddy.xyz) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen       80;
    server_name dev.saikrishnareddy.xyz;
    return 404; # managed by Certbot
}
—--------------------------------------------------------------------------
	Save the file and enable the Nginx Proxy file incase if not enabled.
	Cmd: sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
	Unlink any other files are enabled.
	Cmd: sudo unlink /etc/nginx/sites-enabled/filename
	Step 5 — now restart the nginx service
	Cmd: systemctl restart nginx
	Step 6 —Check the Url in server using curl whether the domain getting healthy or not
	Cmd: curl -I http://rewardx-console-dev.getwalk.in/
Step 5: 
	Enable Firewall to server from DigitalOcean or AWS

```
