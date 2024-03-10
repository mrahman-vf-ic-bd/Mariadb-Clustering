MariaDB Galera Cluster is powered by:
	
	  1. MariaDB Server.
	 2. The Galera wsrep provider library. 
	
**In MariaDB 10.4 and later, MariaDB Galera Cluster uses Galera 4.** 

Before installing mariadb server. Check available version of mariadb in apt:
```
 apt show -a mariadb-server
```

# Allow TCP traffic on ports 3306, 4567, 4568, and 4444
```
sudo ufw allow 3306/tcp
sudo ufw allow 4567/tcp
sudo ufw allow 4568/tcp
sudo ufw allow 4444/tcp
```
# Allow UDP traffic on ports 3306, 4567, 4568, and 4444
```
sudo ufw allow 3306/udp
sudo ufw allow 4567/udp
sudo ufw allow 4568/udp
sudo ufw allow 4444/udp
```

Uncomment  the mandatory setting configuration in
```
/etc/mysql/mariadb.conf.d/60-galera.cnf
```

add following config also
```
wsrep_provider=/usr/lib/galera/libgalera_smm.so
```

Run the following command in that node:
first cluster node just run:
```
sudo galera_new_cluster
```
in other cluster node change the galera.cnf like `first node` and just run following command:

```
sudo systemctl start mariadb
```

Mariadb Cluster:

Server-1:  <server-1-ip> 
Server-2:  <server-2-ip>
Server-3:  <server-3-ip>

1. Installing mariadb in Server-1, Server-2 and Server-3:
```
apt update
sudo apt-get install mariadb-server mariadb-client galera-4 -y
```

Install mysql secure:
```
mysql_secure_installation
```

```
Enter current password for root (enter for none):
Switch to unix_socket authentication [Y/n] n
New password:
Re-enter new password:
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```
End of Mariabd Installation.

2. Now stop mariadb service and do following in all servers.
```
systemctl stop mysql
or
systemctl stop mariadb
```

Next, log in to the server and create a Galera configuration file with the following command:
```
nano /etc/mysql/conf.d/galera.cnf
```

```
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="galera_cluster"
wsrep_cluster_address="gcomm://<server-1-ip>,<server-2-ip>,<server-3-ip>"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="<this-server-ip>"
wsrep_node_name="<this-server-name>"
```

then start the cluster:
```
galera_new_cluster
```

Check the cluster:
```
mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
```



https://www.howtoforge.com/how-to-install-powerdns-on-ubuntu-22-04/ 

Install Master slave dns with powerdns:
https://www.claudiokuenzler.com/blog/844/powerdns-master-slave-dns-replication-mysql-backend 
