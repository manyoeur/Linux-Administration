# Configure a Web Server on Ubuntu (Apache)
## Step 1: Update your system
Before installing anything, make sure your system packages are up to date.
```
sudo apt update
sudo apt upgrade -y
```
## Step 2: Install Apache Web Server
```
sudo apt install apache2 -y
```
After installation, Apache automatically starts.
Check its status:
```
sudo systemctl status apache2
```
You should see "active (running)".
## Step 3: Allow Web Traffic Through Firewall
If you use UFW firewall, allow Apache:
```
sudo ufw allow 'Apache'
sudo ufw enable
sudo ufw status
```
Now ports 80 (HTTP) and 443 (HTTPS) are open.
## Step 4: Test Your Web Server
Open your browser and go to:
```
http://your_server_ip
```
# Configure a Website (Virtual Host)
1. Create a directory for your site
Replace example.com with your domain.
```
sudo mkdir -p /var/www/example.com
```
2. Set ownership

```
sudo chown -R $USER:$USER /var/www/example.com
```
3. Create an index.html page
```
nano /var/www/example.com/index.html
```
Add something like:
```
<h1>Hello from your Ubuntu Web Server!</h1>
```
Save and exit (Ctrl+O, Enter, Ctrl+X).

4. Create a virtual host file
```
sudo nano /etc/apache2/sites-available/example.com.conf
```
5. Paste this:
```
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/example.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
6. Enable the site
```
sudo a2ensite example.com.conf
```
7 Disable the default site (optional):
```
sudo a2dissite 000-default.conf
```
8 Reload Apache
```
sudo systemctl reload apache2
```
# Done!
