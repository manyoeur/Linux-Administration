# How to configure Samba FTP on Ubuntu
<img width="700" height="846" alt="image" src="https://github.com/user-attachments/assets/598c1fdf-e9dc-41fe-9dcf-299b79de24c3" />

## 1. Update your system
```
sudo apt update
sudo apt upgrade -y
```
## 2. Install Samba
```
sudo apt install samba -y
```
Check installation:
```
smbd --version
```
## 3. Create a shared directory
```
sudo mkdir -p /srv/samba/shared
```
Set permissions (optional — adjust as needed):
```
sudo chmod -R 0775 /srv/samba/shared
sudo chown -R nobody:nogroup /srv/samba/shared
```
If you want a specific user to own it:
```
sudo chown -R youruser:youruser /srv/samba/shared
```
## 4. Create a Samba user
Samba uses its own password database.
Add an existing Linux user to Samba:
```
sudo smbpasswd -a youruser
```
Enable the user:
```
sudo smbpasswd -e youruser
```
## 5. Configure Samba share
Edit the Samba config file:
```
sudo nano /etc/samba/smb.conf
```
Add this at the bottom:
```

[SharedFiles]
   path = /srv/samba/shared
   browseable = yes
   read only = no
   writable = yes
   valid users = youruser
```
If you want guest access (no password), use this instead:
```

[Public]
   path = /srv/samba/shared
   browseable = yes
   read only = no
   guest ok = yes
   force user = nobody
```
##  6. Check Samba configuration
```
sudo testparm
```
If you see “Loaded services file OK.” — you're good.
## 7. Restart Samba
```
sudo systemctl restart smbd
sudo systemctl enable smbd
```
## 8. Adjust firewall (if UFW is enabled)
```

sudo ufw allow samba
sudo ufw reload
```
## 9. Connect from Windows
```
\\<Ubuntu-IP>\SharedFiles
```
Example:
```
\\192.168.1.20\SharedFiles
```
## 10. Connect from Linux
```
sudo apt install cifs-utils -y
```
Mount:
```
sudo mount -t cifs //192.168.1.20/SharedFiles /mnt -o username=youruser
```
# ✅ Done!
