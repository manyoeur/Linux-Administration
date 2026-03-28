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
```
### 2.2 Basic Postfix Configuration

Edit `/etc/postfix/main.cf`:
```
sudo nano /etc/postfix/main.cf
```
Common required changes (example from Server World):
```
myhostname = mail.example.com
mydomain = example.com
myorigin = $mydomain
inet_interfaces = all
inet_protocols = ipv4
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8
home_mailbox = Maildir/
smtpd_banner = $myhostname ESMTP
disable_vrfy_command = yes
smtpd_helo_required = yes
message_size_limit = 10240000
```
### 2.3 Enable SMTP AUTH via Dovecot
Add:
```
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
```[3](https://www.server-world.info/en/note?os=CentOS_Stream_9&p=mail&f=1)
```
Restart Postfix:

```
sudo systemctl restart postfix
```
### 2.4 Test Sending Email from Postfix
Install telnet
```
sudo dnf install telnet -y
```
Connect to Postfix
```
telnet localhost 25
```
Then issue SMTP commands:
```
EHLO localhost
MAIL FROM:<test@example.com>
RCPT TO:<you@example.com>
DATA
This is a manual SMTP test.
.
QUIT
```

## 3. Install & Configure Dovecot (IMAP/POP3 Server)
### 3.1 Install Dovecot
```
sudo dnf install -y dovecot
```
Enable and start:
```
sudo systemctl enable --now dovecot
```
### 3.2 Configure Dovecot for Maildir and SASL
Edit /etc/dovecot/dovecot.conf:
```
protocols = imap pop3 lmtp
mail_location = maildir:~/Maildir
```
Edit /etc/dovecot/conf.d/10-auth.conf:
```
disable_plaintext_auth = yes
auth_mechanisms = plain login
```
Enable Postfix integration (create /etc/dovecot/conf.d/10-master.conf entry):
```
unix_listener /var/spool/postfix/private/auth {
  mode = 0660
  user = postfix
  group = postfix
}
```
Restart:
```
sudo systemctl restart dovecot
```
### Test Dovecot IMAP Using Telnet (Port 143)
Connect to IMAP
```
telnet localhost 143
```
Expected output:
```
 OK [CAPABILITY IMAP4rev1 ...] Dovecot ready.
```

## 4. Install Roundcube Webmail
Source includes full Roundcube setup steps for RHEL/CentOS
### 4.1 Install Web & Database Dependencies
```

sudo dnf install -y httpd php php-mysqlnd php-xml php-mbstring php-intl \
php-zip php-gd php-json php-curl mariadb-server
```
Enable MariaDB + Apache:
```
sudo systemctl enable --now mariadb
sudo systemctl enable --now httpd
```
### 4.2 Secure MariaDB
```
sudo mysql_secure_installation
```
### 4.3 Create Roundcube Database
Login:
```
sudo mysql -u root -p
```
SQL:
```

CREATE DATABASE roundcubemail CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'roundcube'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON roundcubemail.* TO 'roundcube'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```[5](https://oneuptime.com/blog/post/2026-03-04-roundcube-webmail-rhel-9-postfix-dovecot/view)

---

## **4.4 Download & Install Roundcube**

```bash
cd /tmp
curl -LO https://github.com/roundcube/roundcubemail/releases/download/1.6.9/roundcubemail-1.6.9-complete.tar.gz
sudo tar xzf roundcubemail-1.6.9-complete.tar.gz
sudo mv roundcubemail-1.6.9 /var/www/roundcube
sudo chown -R apache:apache /var/www/roundcube
```[5](https://oneuptime.com/blog/post/2026-03-04-roundcube-webmail-rhel-9-postfix-dovecot/view)

---

## **4.5 Initialize Roundcube Database**
```bash
sudo mysql -u roundcube -p roundcubemail < /var/www/roundcube/SQL/mysql.initial.sql
```[5](https://oneuptime.com/blog/post/2026-03-04-roundcube-webmail-rhel-9-postfix-dovecot/view)

---

## **4.6 Configure Roundcube**

Copy the sample config:

```bash
sudo cp /var/www/roundcube/config/config.inc.php.sample /var/www/roundcube/config/config.inc.php
sudo vi /var/www/roundcube/config/config.inc.php
```
Set key values:
```

$config['db_dsnw'] = 'mysql://roundcube:your_secure_password@localhost/roundcubemail';

// IMAP via Dovecot
$config['imap_host'] = 'ssl://localhost:993';

// SMTP via Postfix
$config['smtp_host'] = 'tls://localhost:587';
$config['smtp_user'] = '%u';
$config['smtp_pass'] = '%p';

$config['product_name'] = 'Webmail';
```
Restart Apache:
```
sudo systemctl restart httpd
```
Access Roundcube:
```
http://your-server/roundcube
```
## 5. Firewall Configuration
```

sudo firewall-cmd --add-service=smtp --permanent
sudo firewall-cmd --add-service=imap --permanent
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --reload
```
(SMTP example from ServerWorld)
## 6. Optional: DKIM, SPF, DMARC
For better deliverability (from video reference):
Add DNS records SPF, DMARC, DKIM and configure OpenDKIM.
