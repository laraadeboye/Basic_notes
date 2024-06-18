# Comprehensive Guide to NGINX for Junior DevOps Engineers   
AI-GENERATED CONTENT

### Table of Contents
1. Introduction to NGINX
2. Installation and Setup
3. NGINX Directory Structure
4. Basic NGINX Configuration
5. Managing NGINX Service
6. Virtual Hosts (Server Blocks)
7. SSL/TLS Configuration
8. Reverse Proxy Configuration
9. Load Balancing with NGINX
10. Logging and Monitoring
11. Performance Tuning
12. Common Issues and Troubleshooting

1. Introduction to NGINX
NGINX is a high-performance web server, reverse proxy server, and load balancer. It is known for its stability, rich feature set, simple configuration, and low resource consumption.

2. Installation and Setup
Install NGINX on Ubuntu

#
    sudo apt update
    sudo apt install nginx
Verify Installation

#
    nginx -v

3. NGINX Directory Structure
- /etc/nginx/: Main directory for NGINX configuration.
- nginx.conf: Main configuration file.
- sites-available/: Directory for available site configurations.
- sites-enabled/: Directory for enabled site configurations.
- conf.d/: Additional configuration files.
- snippets/: Reusable configuration snippets.

4. Basic NGINX Configuration
Main Configuration File
Located at `/etc/nginx/nginx.conf`. Key sections include:

- worker_processes: Defines the number of worker processes.
- events: Configures worker connections.
- http: Main HTTP server settings.

Example:
```

worker_processes auto;
events {
    worker_connections 1024;
}
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;
    include /etc/nginx/conf.d/*.conf;
}
```

5. Managing NGINX Service
- Start NGINX
#
    sudo systemctl start nginx

- Stop NGINX
#
    sudo systemctl stop nginx

- Restart NGINX
#
    sudo systemctl restart nginx

- Reload NGINX
Useful for applying configuration changes without downtime.

#
    sudo systemctl reload nginx

- Check Status
#
    sudo systemctl status nginx

6. Virtual Hosts (Server Blocks)
In Nginx, a server block is a configuration block that defines a virtual server or a website. It is used to configure settings for a specific domain, subdomain, or IP address. A server block is defined using the server directive and contains various configuration settings, such as:
* listen directive to specify the IP address and port to listen to
* server_name directive to specify the domain or subdomain name
* root directive to specify the document root directory
* index directive to specify the index file
* location blocks to configure specific URL patterns

Server blocks are used to:
* Host multiple websites on a single IP address
* Configure different settings for different domains or subdomains
* Implement virtual hosting
* Configure SSL/TLS settings for secure connections

Define a Server Block
Create a new file in /etc/nginx/sites-available/:

#
    sudo nano /etc/nginx/sites-available/example.com

Example configuration:

```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Enable the Server Block:

Create a symbolic link in `/etc/nginx/sites-enabled/`:

3
    sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/

Test Configuration
#
    sudo nginx -t

Reload NGINX
#
    sudo systemctl reload nginx

7. SSL/TLS Configuration
Obtain SSL Certificates
Use Certbot to obtain free SSL certificates from Let's Encrypt:

#
    sudo apt install certbot python3-certbot-nginx
    sudo certbot --nginx

Example SSL Configuration
Edit the server block to include SSL settings:

```
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    root /var/www/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Reload NGINX
#
    sudo systemctl reload nginx

8. Reverse Proxy Configuration
Example Configuration
Create a server block to proxy requests to another server:

```
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
9. Load Balancing with NGINX
Example Configuration
Create a server block with load balancing:

```
http {
    upstream myapp {
        server 192.168.1.101;
        server 192.168.1.102;
    }

    server {
        listen 80;
        server_name app.example.com;

        location / {
            proxy_pass http://myapp;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```
10. Logging and Monitoring
Access Logs
Default location: `/var/log/nginx/access.log`

Error Logs
Default location: `/var/log/nginx/error.log`

Custom Log Format
Define a custom log format in `nginx.conf`:

```
log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

access_log /var/log/nginx/access.log main;
```
11. Performance Tuning
Worker Processes
Set to the number of CPU cores:

#
    worker_processes auto;

Caching
Enable caching for static files:

```
location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
    expires 7d;
    add_header Cache-Control "public, no-transform";
}
```
Gzip Compression
Enable gzip in `nginx.conf`:

#
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

12. Common Issues and Troubleshooting
Check Configuration
Always test configuration changes:

#
    sudo nginx -t

Check Logs
Examine error logs for issues:

#
    sudo tail -f /var/log/nginx/error.log

Common Issues
- **Port Conflicts**: Ensure no other services are using the same port.
Permission Issues: Check file and directory permissions.
- **Syntax Errors**: Ensure the configuration files have no syntax errors.

### Conclusion
NGINX is a powerful and flexible web server that can be used for a variety of purposes, from serving static files to acting as a reverse proxy and load balancer. By understanding the basics of NGINX configuration and management, you can effectively deploy and maintain web applications. This guide provides a solid foundation for getting started with NGINX, and further exploration of advanced features and best practices will enhance your proficiency.
