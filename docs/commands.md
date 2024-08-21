# Command Examples
To pick up commands quickly by given examples.

## crash - vmcore analyze
```
crash> sys
crash> sys -i
crash> mod -t
crash> bt
crash> log|less
crash> dis -rl <RIP addr>
crash> ps -p 12207
crash> files -d 0xffff9bbxxxxxx
```

## find - command usage examples  

```
$ find . -name "*.conf" -exec ls -l {} \; -exec cat {} \; 
$ find . -type f -path '*para*/*'  -exec ls -l {} \;
$ find . -type -f |xargs grep 'wget'
// quote name if hit find: paths must precede expression
$ find . -name '*results.xml*' -exec readlink -f  {} \;
$ find . -mtime +7 -type d -exec ls {} \;
$ find /dirname -mtime +7 -exec rm -rf {} \;
// echo file name if found
$ find . -name *test_subscription_manager_auto* -exec grep 'PASS' {} +
// top 10 biggest file
$ find /path/to/search/ -type f -iname \"*.mp4\" -printf '%s %p\\n'| sort -nr | head -10
$ find -maxdepth 1 -type f -perm /200 -exec ls -l {} \;
$ find . -type f -writable
```

## firewall-cmd - setup firewall
```
# firewall-cmd --zone=public --add-port=80/tcp --permanent
# firewall-cmd --reload
# iptables-save |grep 80
```
notes: disable selinux if all looks good but not work

## gdb - debug a running python process - [advanced-guide](https://devguide.python.org/advanced-tools/gdb/)
```
# yum install -y gdb
# yum debuginfo-install python311
# gdb -p 9593
# gdb python $pid
(gdb) source /usr/lib/debug/usr/lib64/libpython3.6m.so.1.0-3.6.8-18.el7.x86_64.debug-gdb.py (only required in rhel7)
(gdb) py-bt
(gdb) py-list 
(gdb) info thread
(gdb) thread thread# (switch to thread#)
(gdb) call pthread_exit(0) (exit current thread, but will not work if it is waiting for a lock)
# ldd /usr/bin/gdb | grep python
```

## grubby - update linux boot index and parameters - [official guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-making_persistent_changes_to_a_grub_2_menu_using_the_grubby_tool)

```
# grubby --info=ALL
# grubby --set-default-index=1
# grubby --default-index
# grubby --default-kernel
# grubby --update-kernel=ALL --args='console=ttyS0'
# grubby --update-kernel=ALL --args='initcall_debug no_console_suspend ignore_loglevel earlyprintk=ttyS0 debug'
# grubby --update-kernel=ALL --args='loglevel=7 debug'
# grubby --update-kernel=ALL  --remove-args='rhgb quite'
# grubby --update-kernel=ALL --args='initcall_blacklist=arm_smmu_driver_init'
# grubby --update-kernel=ALL  --remove-args='kmemleak=on'
# grubby --update-kernel=ALL --args='no_console_suspend'
# grubby --update-kernel=ALL --args='intel_iommu=on pci=realloc'
# grubby --update-kernel=ALL --remove-args='initcall_blacklist=arm_smmu_driver_init'
# grubby --update-kernel=ALL --args='rd.blacklist=nouveau'
```

## insights-client - Red Hat Insights Client tool 
```
# insights-client --register
# insights-client --check-result
# insights-client --show-result
# insights-client --no-upload --keep-archive
```

## journalctl - control the journal log size and others

```
# journalctl --vacuum-time=10d
# journalctl --vacuum-size=2G
# journalctl --rotate
# journalctl --vacuum-time=1s
# journalctl --since today
# journalctl --since '2019-01-30 14:00:00'
// this boot's log only
# journalctl -b 0
# du -hs /var/log/journal
# journalctl --output=verbose --grep userfaultfd --boot
# journal -u nm-cloud-setup
```

## passwd - update password without interaction
```
# newpass=$(openssl rand -base64 8)
# echo $newpass|passwd $user --stdin
```

## subscription-manager - registers systems to a subscription management service
```
# subscription-manager register --username=XXXX --password=XXXX--auto-attach --force
# subscription-manager status
# dnf repolist all
# subscription-manager repos --enable cert-1-for-rhel*
```
Note: [Subscription-manager command fails with "NetworkException: Network error code: 400"](https://access.redhat.com/solutions/5187461)

## tmux — terminal multiplexer
```
# yum install -y tmux
# tmux - start a new session
# Ctrl+b d - detach current session
# tmux attach -t 0 - attach to session 0
# Ctrl+b [0~9] - Select window 0~9
# Ctrl+b s - list all sessions
# Ctrl+b ? - list all options
# Ctrl+b \" - split window horizontally
# Ctrl+b % - split window vertically
# Ctrl+b x - kill a pane"
# Ctrl+b UP/Down - move cursor between split window
# Ctrl+b [ - scroll up to see more log
# tmux ls
```