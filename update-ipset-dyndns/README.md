# update-ipset-dyndns

A small shell script that reads hostnames from a file, resolves them to IP  
addresses and adds them to an ipset. This allows for a static firewall /  
iptables configuration that gets updated dynamically by a cronjob.  
Useful if you've got an IP that changes often but you don't want to  
leave some ports on your server unprotected.  
  
Save the script to `/usr/local/bin/update-ipset-dyndns.sh`, then add an  
iptables rule like this in your firewall (this would be for ssh/22, change to your liking):  
  
```sh
# match set "set-dynip" and allow traffic from those hosts to ssh
iptables -A INPUT -p tcp -m set --match-set set-dynip src -m tcp --dport 22 -m multiport --sports 1024:65535,22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT

# allow all established traffic
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

# drop everything else
iptables -A INPUT -p tcp -m tcp --dport 22 -j REJECT --reject-with icmp-host-prohibited
```
  
To install the cronjob file just copy the contents of `update-ipset-dyndns.crond`  
to `/etc/cron.d/update-ipset-dyndns`.  
All done!
