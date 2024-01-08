# Check for space issues

`strace -e statfs df /`   # disk space available 

`sudo du -sh * 2>/dev/null | sort -h`  # Find the largest directories under the parent dir

# Core Dumps
```
sudo sysctl -w kernel.core_pattern=core.%u.%p.%t
systemctl restart apport
ulimit -c unlimited
ulimit -a
```

# Delete all Docker stuff including volumes
```
sudo su
# service docker stop
# cd /var/lib/docker
# rm -rf *
# service docker start
```

