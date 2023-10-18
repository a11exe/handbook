# Nginx

A reverse proxy is the recommended method to expose an application server to the internet.

### Install Nginx
```
sudo apt update
sudo apt install nginx
```

### Configure Nginx
It is recommended practice to create a custom configuration file for your new server block additions, instead of editing the default configuration directly. 
Create and open a new Nginx configuration file using nano or your preferred text editor:

```
sudo nano /etc/nginx/sites-available/your_domain
```

Insert the following into your new file, making sure to replace your_domain and app_server_address

```
http {
   upstream app{
      server 10.2.0.100;
      server 10.2.0.101;
      server 10.2.0.102;
   }

   server {
      listen 80;
      
      server_name mydomain.com;

      location / {
          include proxy_params;
          
          proxy_pass http://app;
          

          proxy_redirect off;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
      }
   }
}
```

This configuration file begins with a standard Nginx setup, where Nginx will listen on port 80 and respond to requests made to your_domain and www.your_domain. 
Reverse proxy functionality is enabled through Nginx’s proxy_pass directive.

All HTTP requests come with headers, which contain information about the client who sent the request.
Nginx provides some recommended header forwarding settings you have included as proxy_params, and the details can be found in `/etc/nginx/proxy_params`:

```
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

By default, when Nginx acts as a reverse proxy it alters two headers, strips out all empty headers, then passes on the request. 
The two altered headers are the Host and Connection header.

Next, enable this configuration file by creating a link from it to the sites-enabled directory that Nginx reads at startup:

```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

You can now test your configuration file for syntax errors:

```
sudo nginx -t
```

With no problems reported, restart Nginx to apply your changes:
```
sudo systemctl restart nginx
```

### Two locations
The proxy_pass directive forwards HTTP requests to a specified network address

```
server {
   listen 8000;
   location /api {
      proxy_pass http://127.0.0.1:8081/client1;
   }

   location /api/client2 {
      proxy_pass http://127.0.0.1:8082/client2;
   }
}
```