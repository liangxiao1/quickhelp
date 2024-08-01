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