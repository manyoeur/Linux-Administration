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
