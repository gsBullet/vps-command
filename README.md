<h1 align="center">VPS COMMAND</h1>

# AWS Ubuntu Server Setup Lesson Outline
# 1. AWS Account Preparation

Create an AWS account if you don't have one

      -Go to AWS Management Console
      -Navigate to EC2 service

#2. Launch an Ubuntu EC2 Instance
 EC2 Instance Creation Steps:
 1. Choose Ubuntu Server LTS (Long Term Support) AMI
 2. Select instance type (t2.micro is free tier eligible)
 3. Configure security group to allow:
    
        - SSH (Port 22)
        - HTTP (Port 80)
        - HTTPS (Port 443)
        - Custom TCP (Port 3000 for Node.js)

# 3. Initial Server Setup Commands

Update package lists

     sudo apt update && sudo apt upgrade -y

 Install essential tools
 
    sudo apt install -y curl git wget software-properties-common

# 4. Node.js and NPM Installation

 Install Node.js using NodeSource repository

    curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash -
    sudo apt install -y nodejs

 Verify installations
 
    node --version
    npm --version

# 5. Install Nginx (Reverse Proxy)

        sudo apt install nginx -y

Configure Nginx to forward requests to Node.js app

    sudo nano /etc/nginx/sites-available/default

Nginx Configuration Example:

    server {
    listen 80;
    server_name your_domain_or_ip;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    }

NGINX command

      sudo systemctl start nginx
      sudo systemctl status nginx
      sudo systemctl enable nginx
      sudo systemctl stop nginx
      sudo systemctl restart nginx

  
# 6. Process Management with PM2

 Install PM2 globally
 
    sudo npm install -g pm2

 Start your app with PM2
 
    pm2 start server.js
    pm2 startup systemd
    sudo pm2 startup
    pm2 save
    
Start the Next.js app with PM2:

      pm2 start npm --name "product-portfolio" -- run dev --watch


# 7. SSL/HTTPS Setup with Certbot

 Install Certbot
 
    sudo apt install certbot python3-certbot-nginx -y
 Obtain SSL certificate
 
    sudo certbot --nginx -d your_domain.com
    
# 8. Firewall Configuration

Install UFW (Uncomplicated Firewall)

    sudo apt install ufw -y
Allow necessary ports

    sudo ufw allow ssh
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw allow 3000/tcp
    
education purpose you can enable ufw  but when it use it production just allow port 

    sudo ufw enable

# add rules in aws very important, must check this to get firewall
# 9. Git and Project Deployment

 Clone your project repository
 
    git clone your_project_repository_url
    cd project_directory

 Install dependencies
 
    npm install

Set environment variables

    export NODE_ENV=production
    export JWT_SECRET=your_secure_secret_key


# Security Lessons and Best Practices

# Never expose sensitive credentials in code
    Use environment variables
    Keep software updated
    Implement proper authentication
    Use HTTPS
    Limit server access
    Regular backups
    Monitor server logs

# Common Challenges and Solutions

    Permissions: Use sudo carefully
    Firewall: Ensure correct port configurations
    Dependencies: Use npm ci for production
    Environment: Manage with .env files

# Recommended Tools

-AWS Elastic IP (static IP)
-AWS Security Groups
-CloudWatch for monitoring
-AWS Secrets Manager for credential management

# Cost Optimization

-Use AWS Free Tier
-Stop/Terminate instances when not in use
-Choose appropriate instance types

# Deployment Workflow

Typical deployment process

    git pull origin main
    npm install
    pm2 restart server
    
# recommended Additional Security

    -Implement fail2ban for intrusion prevention
    -Regular security audits
    -Use SSH key-based authentication
    -Disable root login

Monitoring and Logging

# View PM2 logs
    pm2 logs

# System logs
    journalctl -u nginx

# apache server setup

      
      <VirtualHost *:80>
      	ServerName example.com
      	ProxyPreserveHost on
      	ProxyPass / http://localhost:3005/
      	ProxyPassReverse / http://localhost:3005/
      	ErrorLog ${APACHE_LOG_DIR}/error.log
      </VirtualHost>
      <VirtualHost *:80>
      	ServerName  backend.example.com
      	ProxyPreserveHost on
      	ProxyPass / http://localhost:5053/
      	ProxyPassReverse / http://localhost:5053/
      	ErrorLog ${APACHE_LOG_DIR}/error.log
      </VirtualHost>


# extra for IP

      <VirtualHost *:80>
          ServerName Your IP
      
          ProxyPreserveHost On
      
          # Proxy requests to Next.js
          ProxyPass / http://localhost:3000/
          ProxyPassReverse / http://localhost:3000/
      
          # WebSocket Support
          RewriteEngine On
          RewriteCond %{HTTP:Upgrade} =websocket [NC]
          RewriteRule /(.*) ws://localhost:3000/$1 [P,L]
      
          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>


# Enable the Site and Reload Apache2


      sudo a2ensite product-portfolio.conf
      sudo systemctl reload apache2

# Estimated Time Investment

    -Initial Setup: 1-2 hours
    -Configuration: 2-3 hours
    -Security Hardening: 1-2 hours

# Learning Objectives

     -Cloud server management
     -Linux system administration
     -Web application deployment
     -Security best practices
     -Nginx configuration
     -Process management
     -SSL implementation

# Recommended Next Steps

      -Learn Docker for containerization
      -Explore CI/CD pipelines
      -Study advanced security techniques
      -Understand load balancing
