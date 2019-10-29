# Roger-skyline-1

Hello, today we're going to learn how to setup a small server, with a protection against DOS attack and a protection against port scaning. We'll also discover how to properly create a public and a private key to connect via ssh and disable connecting via ssh with a password. And one more thing, we will make our server's ip address static.

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
In this part you must head for a file ```/etc/netplan/50*```. The name might be different from a OS to another but don't panic it will be the same syntax.
You will have somthing like this :


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
Head for ```/etc/ssh```
