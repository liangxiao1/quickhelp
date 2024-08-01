# Deploy Services

## Linux cockpit web console  
```
# yum install -y cockpit cockpit-machines libvirt
# systemctl enable --now libvirtd
# systemctl disable --now firewalld
# systemctl enable --now cockpit
# sed  -i 's/^root/#root/g' /etc/cockpit/disallowed-users
```
Now you have it online: https://hostname:9090/.