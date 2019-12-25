# Roger-skyline-1

Hello, today we're going to learn how to setup a small server. We'll discover how to properly create a public and a private key to connect via ssh and disable connecting via ssh with a password. And one more thing, we will make our server's ip address static.

I worked on a ubuntu server, I recommend it, it's pretty good and simple to use.
```
OS        : Ubuntu.
Version   : 18.04.3.
VM driver : VirtualBox.
Disk      : 8.0GB with a 4.2GB partition.
RAM       : 1GB
```
(always working in the virtual machine)
First of all the ip address.
In this part you must head for a file ```/etc/netplan/50-cloud-init.yaml```. The name might be different from a OS to another but don't panic it will be the same syntax.
You will have somthing like this :
```
blablabla
      dhcp4: true (or yes)
blablabla
```
change it to this :
```
flaflafla
      dhcp4: no
      addresses: [your address with netmask]
      gateway4: your gateway
      #if necessary
      nameservers:
        addresses: [8.8.8.8] #google's dns server :D
flaflafla
```
then apply the changes by : ```sudo netplan apply```
and boom you have your static ip address.


Now for the connection via ssh:
First let's create the publice and the private key:
```ssh-keygen```
then head for ```~/.ssh/``` you will find 3 files ```id_rsa``` ```id_rsa.pub``` ```authorized_keys```
just cat the ```id_rsa.pub``` in the ```authorized_keys``` file with this command ```cat id_rsa.pub >> authorized_keys```.
what we just did is : we create a new pubkeys then we authorized to be able to connect using the private key which is named ```id_rsa``` if you cat it you will have somthing like this :
```
-----BEGIN RSA PRIVATE KEY-----
bunch of characteres
-----END RSA PRIVATE KEY-----
```
it's a way of encryption. FYI : you can use RSA encryption in encrypting data too. If you watched Mr. Robot you'll find ```spoiler``` that Elliot sent to the FBI a RSA PRIVATE KEY to decrypt all E.Corp's data.
Now let's disable the password authentication and enable the pubkey authentication.
Head for ```/etc/ssh```. Now under sudo type ```vim sshd_config```.
Search for :
```
#PubkeyAuthentication yes
```
remove the dash only don't change anything else.
also
```
PasswordAuthentication yes
```
if you had a dash there remove it and change yes to no.
save the file and restart ssh service : ```sudo service sshd restart```
Addition : if you want to change you current ssh port you should look for ```Port 2710``` in the same file, remove the dash if you have one and change the port number. Don't forget to restart your service after.
Now go back to ```~/.ssh/``` file and copy the private key ```id_rsa``` to your Mac or windows or whatevering is your host.
Now you can be able to connect via ssh like this : ```ssh -i /path/to/id_rsa user@server's_ip -p port``` and done. Now you can share the private key we created for people to connect to your server via ssh. It's fun right ?
PS: if you want people not to be able to connect via ssh as root even if they have the password just look for ```PermitRootLogin no``` remove the dash if it exists and change whatever you have to ```no```. Don't forget to restart your service :o.

After protecting our connection with RSA encryption via SSH. Now let's protect our webserver(server) from DOS attack and Port scanning.
<h3>DOS Attack</h3>
I used ```iptables``` for this :

```
iptables -F #flushign all current rules

#allowing established and related connection
#established : are connections that have been seen in either ways (input or output)
#related : are new trusted connection (like watching a youtube video)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

#allowing ports : DNS, HTTP, HTTPS, SSH
sudo iptabels -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptabels -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptabels -A INPUT -p tcp --dport [SSH_PORT] -j ACCEPT
sudo iptabels -A INPUT -p tcp --dport 53 -j ACCEPT
sudo iptabels -A INPUT -p udp --dport 53 -j ACCEPT

#droping packets that were seen 10 times in the last 60 seconds(new connections only)
sudo iptables -I INPUT -m state --state NEW -m recent --set
sudo iptables -I INPUT -m state --state NEW -m recent --update --seconds 60 --hitcount 10 -j DROP

#drop by default
sudo iptables -P INPUT DROP
sudo iptables -P FORWORD DROP
```
