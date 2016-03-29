
#Install Postfix

> Reference: Digital Ocean's [How To Install and Setup Postfix on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-setup-postfix-on-ubuntu-14-04)

The installation process of Postfix on Ubuntu 14.04 is easy because the software is in Ubuntu's default package repositories.

Since this is our first operation with `apt` in this session, we're going to update our local package index and then install the Postfix package:


```
sudo apt-get install mailutils
sudo apt-get install postfix
```


#How To Install and Setup Postfix on Ubuntu 14.04

##Introduction

Postfix is a very popular open source Mail Transfer Agent (MTA) that can be used to route and deliver email on a Linux system. It is estimated that around 25% of public mail servers on the internet run Postfix.

In this guide, we'll teach you how to get up and running quickly with Postfix on an Ubuntu 14.04 server.

###Prerequisites
In order to follow this guide, you should have a Fully Qualified Domain Name pointed at your Ubuntu 14.04 server. You can find help on setting up your domain name by clicking [here](dns-setup.md).

###Uninstall all other mail agents
If sendmail is installed we're going to uninstall it:

```
apt-get purge sendmail*
```

##Install the Software
The installation process of Postfix on Ubuntu 14.04 is easy because the software is in Ubuntu's default package repositories.

Since this is our first operation with apt in this session, we're going to update our local package index and then install the Postfix package:

```
sudo apt-get update
sudo apt-get install postfix
```

You will be asked what type of mail configuration you want to have for your server. For our purposes, we're going to choose "Internet Site" because the description is the best match for our server.

Next, you will be asked for the Fully Qualified Domain Name (FQDN) for your server. This is your full domain name (like `example.com`). Technically, a FQDN is required to end with a dot, but Postfix does not need this. We will use our mail domain. So we can just enter it like:

```
archimedes.ptechinnovations.com
```
For the rest of the options just accept the defaults. The configuration will be done in greater detail in the next stage.


##Configure Postfix
From a terminal prompt:

`sudo dpkg-reconfigure postfix`

We are going to need to change some basic settings in the main Postfix configuration file.

Insert the following details when asked (replacing server1.example.com with your domain name if you have one):

1. General type of mail configuration: Internet Site
2. NONE doesn't appear to be requested in current config
3. System mail name:`archimedes.ptechinnovations.com`
4. Root and postmaster mail recipient: `server`
5. Other destinations for mail: ` `
6. Force synchronous updates on mail queue?: `No`
7. Local networks: 127.0.0.0/8
8. Yes doesn't appear to be requested in current config
9. Mailbox size limit (bytes): `0`
10. Local address extension character: `+`
11. Internet protocols to use: `all`

Now Let's configure this file even more to handle DKIM key and SPF. 

Begin by opening this file with root privileges in your text editor:

`sudo nano /etc/postfix/main.cf`

First, we need to find the myhostname parameter. During the configuration, the FQDN we selected was added to the mydestination parameter, but myhostname remained set to localhost. We want to point this to our FQDN too:

`myhostname = archimedes.ptechinnovations.com`

If you would like to configuring mail to be forwarded to other domains or wish to deliver to addresses that don't map 1-to-1 with system accounts, we can remove the `alias_maps` parameter and replace it with `virtual_alias_maps`. We would then need to change the location of the hash to `/etc/postfix/virtual`:

`virtual_alias_maps = hash:/etc/postfix/virtual`

As we said above, the `mydestination` parameter has been modified with the FQDN you entered during installation. This parameter holds any domains that this installation of Postfix is going to be responsible for. It is configured for the FQDN and the localhost.

One important parameter to mention is the mynetworks parameter. This defines the computers that are able to use this mail server. It should be set to local only (`127.0.0.0/8` and the other representations). Modifying this to allow other hosts to use this is a huge vulnerability that can lead to extreme cases of spam.

To be clear, the line should be set like this. This should be set automatically, but double check the value in your file:

`mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128`

**Next** Add all the fields not in your file:
Don't forget the `DKIM` lines at the bottom.

```
# See /usr/share/postfix/main.cf.dist for a commented, more complete version


# Debian specific:  Specifying a file name will cause the first
# line of that file to be used as the name.  The Debian default
# is /etc/mailname.
#myorigin = /etc/mailname

smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)
biff = no

# appending .domain is the MUA's job.
append_dot_mydomain = no

# Uncomment the next line to generate "delayed mail" warnings
#delay_warning_time = 4h

readme_directory = no

# TLS parameters
smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
smtpd_use_tls=yes
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

# See /usr/share/doc/postfix/TLS_README.gz in the postfix-doc package for
# information on enabling SSL in the smtp client.

smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
myhostname = archimedes.ptechinnovations.com
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = /etc/mailname
mydestination =
relayhost =
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = loopback-only
inet_protocols = all

# DKIM
# --------------------------------------
milter_default_action = accept
milter_protocol = 2
smtpd_milters = inet:localhost:8891
non_smtpd_milters = inet:localhost:8891
```





Install MailUtils via `sudo apt-get install mailutils` 

This will give you access to the `mail` program that you can interact with your mail.

Restart Postfix:
  
```
sudo service postfix restart
```

If sendmail was installed at one point, you may get this error:
```
public/pickup: No such file or directory
```

Run this command to fix the error

```
sudo mkfifo /var/spool/postfix/public/pickup
sudo service postfix restart
```

Next proceed to [Setting Up SPF and DKIM for an Ubuntu 14.04 Mail Server](setup-spf-dkim.md).



