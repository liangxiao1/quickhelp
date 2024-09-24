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

## NFS service setup and export
```
# yum install -y nfs-utils
# systemctl enable --now rpcbind nfs-server
# exportfs -o ro,insecure_locks,all_squash,fsid=1 *:/
// option fore automount
# echo "$nfs_ip:/$dir /$target_dir nfs    defaults    0 0\" >>/etc/fstab

```

## Create your own systemd service unit file

Put your own service unit file under "/usr/lib/systemd/system/".  
```
// rhcert-manager.service
[Unit]
Description=Refresh rhcert token

[Service]
StandardError=journal
ExecStart=bash -c "/home/p3_venv/bin/python  /home/p3_venv/bin/rhcert_manager token --refresh"

[Install]
WantedBy=multi-user.target

// rhcert-manager.timer
[Unit]
Description=Refresh rhcert token every 120 mins

[Timer]
OnBootSec=5min
OnUnitActiveSec=120min
Unit=rhcert-manager.service

[Install]
WantedBy=timers.target
```
Note: [systemd.unit — Unit configuration](https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html)