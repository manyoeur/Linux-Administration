# How to install mail Server in CentOS
## Prerequisites
Before starting, ensure:

* A CentOS Stream 9 server
* Root/sudo access
* A fully qualified domain name (FQDN) pointing to your server
* Correct DNS records (A + MX) [reintech.io]
* Open port 25 (SMTP), 143/993 (IMAP), 80/443 (HTTP/HTTPS)

Update packages:
```
sudo dnf -y update
```
## 2. Install & Configure Postfix (SMTP Server)
### 2.1 Install Postfix
CentOS 9 includes Postfix in its repo:
```
sudo dnf install -y postfix
```
Start and enable it:
```
sudo systemctl start postfix
sudo systemctl enable postfix
```[1](https://reintech.io/blog/install-configure-postfix-centos-9)
---

### **2.2 Basic Postfix Configuration**

Edit `/etc/postfix/main.cf`:

```bash
sudo vi /etc/postfix/main.cf
```
