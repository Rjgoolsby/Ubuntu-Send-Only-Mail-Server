Setting the Hostname
====================
> Reference: Linode's [Set Host Name ](https://www.linode.com/docs/getting-started)

You’ll need to set your system’s hostname and fully qualified domain name (FQDN). Your hostname should be something unique. Some people name their servers after planets, philosophers, or animals. Note that the system’s hostname has no relationship to websites or email services hosted on it, aside from providing a name for the system itself. Your hostname should not be “www” or anything too generic. 

I personally like Greek & Roman Mythology so all my names are related. It's a good idea to make your server name and host name the same. This is helpful when you're managing multiple servers. 

You will need to perform all these tasks as root user by either prefixing all commands with `sudo` or entering root mode using `sudo -s`.

You’ll need to set your system’s hostname and fully qualified domain name (FQDN). Your hostname should be something unique. Some people name their servers after planets, philosophers, or animals. Note that the system’s hostname has no relationship to websites or email services hosted on it, aside from providing a name for the system itself. Your hostname should not be “www” or anything too generic.

Once you’re done, you can verify by running the command `hostname`.

>**Note**  
>If you’re unfamiliar with Linux, one of the first things you’ll need to learn is how to use nano, a text editor included with most distributions. To open a file for editing, type `nano file.txt` where “file.txt” is the name of the file you want to create or edit. If the file is not in your current working directory, specify the entire file path. For example, open the `hosts` file with:
>
`nano /etc/hosts`  
When you’re finished editing, press Control-X, then Y to save the changes >and Enter to confirm.


Update /etc/hosts
=================

Update the `/etc/hosts` file. This file creates static associations between IP addresses and hostnames, with higher priority than DNS.

1. Every hosts file should begin with the line `127.0.0.1 localhost.localdomain localhost`, although the naming may be slightly different between Linux distributions. `127.0.0.1` is the loopback address, and is used to send IP traffic internally on the system. You can leave this line alone.
  
  Some distributions may also ship with a line for `127.0.1.1` in their `hosts file`. This is the loopback domain, and can be ignored in most cases.

2. Add a line for your Linode’s public IP address. You can associate this address with your Linoode’s **Fully Qualified Domain Name** (FQDN) if you have one, and with the local hostname you set in the steps above. In the example below, `203.0.113.10` is our public IP address, `hostname` is our local hostname, and `hostname.example.com` is our FQDN.


As with the hostname, the domain name part of your FQDN does not necessarily need to have any relationship to websites or other services hosted on the server (although it may if you wish). As an example, you might host “www.something.com” on your server, but the system’s FQDN might be “mars.somethingelse.com.”

File : **/etc/hosts**  

1. `127.0.0.1 localhost.localdomain localhost`
2. `203.0.113.10 hostname.example.com hostname` 

If you have IPv6 enabled on your Linode, you may also want to add an entry for your IPv6 address, as shown in this example:

File: **/etc/hosts**

```
127.0.0.1 localhost.localdomain localhost
203.0.113.10 hostname.example.com hostname
2600:3c01::a123:b456:c789:d012 hostname.example.com hostname
```

The value you assign as your system’s FQDN should have an “A” record in DNS pointing to your Linode’s IPv4 address. For Linodes with IPv6 enabled, you should also set up a “AAAA” record in DNS pointing to your Linode’s IPv6 address. For more information on configuring DNS, see [Adding DNS Records](https://www.linode.com/docs/hosting-website#sph_adding-dns-records).

Setting the Timezone
====================
By default, a Linux image will be set to UTC time (also known as Greenwich Mean Time) but this can be changed. It may be better to use the same timezone which a majority of your users are located in, or that you live in to make log file timestamps more sensible.

`dpkg-reconfigure tzdata`


Demostration of Instructions
==============
**First** let's set your hostname for your server.  We'll use these parameters for the rest of this tutorial:

```
Hostname: archimedes  
IP4: 203.0.113.101
IP6: 2600:3c01::a123:b456:c789:d012
```
From your terminal prompt. 

```
echo "archimedes" > /etc/hostname
hostname -F /etc/hostname
```
**Next** we need to edit our `hosts` file.  

From your terminal prompt. 

`nano /etc/hosts`

Your file should look similiar to this.

```
127.0.0.1 localhost

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

```
Next we're going to add our hostname and server's public ip addresses.  

```
203.0.113.10    archimedes archimedes.ptechinnovations.com
2600:3c01::a123:b456:c789:d012 archimedes archimedes.ptechinnovations.com
```

**This is important**: We're also going to add our mailing domain along with local.domain. This is important for our mailing server. Add your mailing domain to and local.domain to your hosts. 

```
127.0.0.1       localhost localhost.localdomain
```

Your completed file should look like 

```
127.0.0.1       localhost localhost.localdomain
127.0.1.1       ubuntu
203.0.113.10    archimedes archimedes.ptechinnovations.com
2600:3c01::a123:b456:c789:d012 archimedes archimedes.ptechinnovations.com


# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```

When you’re finished editing, press Control-X, then Y to save the changes and Enter to confirm.


**Next** Proceed to [Install PostFix](install-postfix.md)