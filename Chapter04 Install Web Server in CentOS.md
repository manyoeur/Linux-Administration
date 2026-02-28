# Install Apache Web Server on CentOS / RHEL
## 1️⃣ Update your system
```
sudo yum update -y
```
## 2️⃣ Install Apache (httpd)
```
sudo yum install httpd -y
```
Check installation:
```
httpd -v
```
## 3️⃣ Start and enable Apache
Start the Apache service:
```
sudo systemctl start httpd
```
Enable at system boot:
```
sudo systemctl enable httpd
```
Check status:
```
sudo systemctl status httpd
```
## 4️⃣ Configure Firewall (if firewall is running)
Allow HTTP (port 80):
```
sudo firewall-cmd --permanent --add-service=http
```
Allow HTTPS (port 443):
```
sudo firewall-cmd --permanent --add-service=https
```
Reload firewall:
```
sudo firewall-cmd --reload
```
## 5️⃣ Verify Apache is running
```
http://localhost
```
or
```
http://your_ip_address
```
You should see the Apache Test Page.
To check port 80:
```
sudo ss -tulnp | grep httpd
```
## 6️⃣ Apache configuration files
Main configuration file:
```
/etc/httpd/conf/httpd.conf
```
Supplemental configs:
```
/etc/httpd/conf.d/*.conf
```
Document root (default website directory):
```
/var/www/html/
```
## 7️⃣ Create a simple test webpage
```
h1>Apache Web Server Running Successfully</h1>" | sudo tee /var/www/html/index.html
```
Restart Apache:
```
sudo systemctl restart httpd
```
## 8️⃣ Manage Apache service
Stop Apache:
```
sudo systemctl stop httpd
```
Restart Apache:
```
sudo systemctl restart httpd
```
Reload configuration without restarting:
```
sudo systemctl reload httpd
```
## 9️⃣ Enable SELinux rules (Only if SELinux is enforcing)
To allow Apache to read files outside default directory:
```
sudo setsebool -P httpd_read_user_content 1
```
For allowing network connections (e.g., reverse proxy):
```
sudo setsebool -P httpd_can_network_connect on
```
# ✅ Done!

# Create Apache Virtual Hosts
A Virtual Host lets one Apache server host multiple websites on a single machine—each with its own domain and document root.
Example:

* example.com → /var/www/example.com/
* test.com → /var/www/test.com/
## 1. Create document root directories
Example for two domains:
```

sudo mkdir -p /var/www/example.com/public_html
sudo mkdir -p /var/www/test.com/public_html
```
## 2. Set ownership and permissions
```

sudo chown -R $USER:$USER /var/www/example.com/public_html
sudo chown -R $USER:$USER /var/www/test.com/public_html
sudo chmod -R 755 /var/www
```
## 3. Create test index pages
```

cho "<h1>Welcome to example.com</h1>" | sudo tee /var/www/example.com/public_html/index.html
echo "<h1>Welcome to test.com</h1>" | sudo tee /var/www/test.com/public_html/index.html
```
## 4. Create Virtual Host configuration files
On CentOS/RHEL, virtual host files usually go in:
```
/etc/httpd/conf.d/
```
### Example Virtual Host for example.com
Create file:
```
sudo nano /etc/httpd/conf.d/example.com.conf
```
Add:
```

<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/example.com/public_html

    ErrorLog /var/log/httpd/example.com-error.log
    CustomLog /var/log/httpd/example.com-access.log combined
</VirtualHost>
```
### Virtual Host for test.com
```
sudo nano /etc/httpd/conf.d/test.com.conf
```
Add:
```

<VirtualHost *:80>
    ServerName test.com
    ServerAlias www.test.com
    DocumentRoot /var/www/test.com/public_html

    ErrorLog /var/log/httpd/test.com-error.log
    CustomLog /var/log/httpd/test.com-access.log combined
</VirtualHost>
```
## 5. Test Apache configuration
```
sudo apachectl configtest
```
Expected:
```
Syntax OK
```
## 6. Restart Apache
```
sudo systemctl restart httpd
```
## 7. Add DNS or local test (optional)
If DNS isn’t set yet, you can test locally by editing:
```
sudo nano /etc/hosts
```
Add:
```
127.0.0.1   example.com
127.0.0.1   test.com
```
Then visit in your browser:
```
http://example.com
http://test.com
```
# ✅ Done!
