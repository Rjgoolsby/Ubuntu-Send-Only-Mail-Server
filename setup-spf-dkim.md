#Setting Up SPF and DKIM for an Ubuntu 14.04 Mail Server
> Reference: ExRatione's [Setting Up SPF and DKIM for an Ubuntu 14.04 Mail Server](https://www.exratione.com/2014/07/setting-up-spf-and-dkim-for-an-ubuntu-1404-mail-server)

DomainKeys Identified Mail (DKIM)

Setting up DKIM is a little more involved than SPF, but still not too challenging if you are already running a Postfix mail server on Ubuntu. First install the necessary packages:

```
apt-get install opendkim opendkim-tools
```

#### Edit opendkim.conf file

```
nano /etc/opendkim.conf
```

Add the following to `/etc/opendkim.conf`:   

```
Domain    archimedes.ptechinnovations.com
KeyFile    /etc/postfix/dkim.key
Selector    dkim
SOCKET    inet:8891@localhost
```

Add anything else your missing to your file. Your file should look like this:

```
# This is a basic configuration that can easily be adapted to suit a standard
# installation. For more advanced options, see opendkim.conf(5) and/or
# /usr/share/doc/opendkim/examples/opendkim.conf.sample.

# Log to syslog
Syslog                  yes
# Required to use local socket with MTAs that access the socket as a non-
# privileged user (e.g. Postfix)
UMask                   002

# Sign for example.com with key in /etc/mail/dkim.key using
# selector '2007' (e.g. 2007._domainkey.example.com)
Domain                  archimedes.ptechinnovations.com
KeyFile                 /etc/postfix/dkim.key
Selector                dkim
SOCKET                  inet:8891@localhost

# Commonly-used options; the commented-out versions show the defaults.
#Canonicalization       simple
#Mode                   sv
#SubDomains             no
#ADSPAction            continue

#From Ubuntu Wiki
AutoRestart             yes
Background              yes
Canonicalization        relaxed/relaxed
DNSTimeout              5
Mode                    sv
SignatureAlgorithm      rsa-sha256
SubDomains              no
#UseASPDiscard          no
#Version                rfc4871
X-Header                no

# Always oversign From (sign using actual From and a null From to prevent
# malicious signatures header fields (From and/or others) between the signer
# and the verifier.  From is oversigned by default in the Debian pacakge
# because it is often the identity key used by reputation systems and thus
# somewhat security sensitive.
OversignHeaders         From

# List domains to use for RFC 6541 DKIM Authorized Third-Party Signatures
# (ATPS) (experimental)

#ATPSDomains            example.com
#From Ubuntu Wiki
UserID                  opendkim:opendkim
KeyTable                /etc/opendkim/KeyTable
SigningTable            /etc/opendkim/SigningTable
ExternalIgnoreList      /etc/opendkim/TrustedHosts
InternalHosts           /etc/opendkim/TrustedHosts
#

```

We next need to create the opendkim folder 

```
mkdir /etc/opendkim
```


####Create the Trusted Hosts file

```
nano /etc/opendkim/KeyTable
```
Your file should look like this (remember your server name and ip address from [Set Host Name](set-host-name.md):

```
127.0.0.1
localhost
archimedes.ptechinnovations.com
203.0.113.10
```
#### Edit opendkim file

```
nano /etc/default/opendkim
```

Uncomment this row and use port 8891:

```
SOCKET="inet:8891@localhost" # listen on loopback on port
```

Your file should look like this:

```
# Command-line options specified here will override the contents of
# /etc/opendkim.conf. See opendkim(8) for a complete list of options.
#DAEMON_OPTS=""
#
# Uncomment to specify an alternate socket
# Note that setting this will override any Socket value in opendkim.conf
#SOCKET="local:/var/run/opendkim/opendkim.sock" # default
#SOCKET="inet:54321" # listen on all interfaces on port 54321
SOCKET="inet:8891@localhost" # listen on loopback on port 12345
#SOCKET="inet:12345@192.0.2.1" # listen on 192.0.2.1 on port 12345
```

#### Make sure your firewall (iptables) allows loopback on localhost:

Run this command

```
sudo iptables -A INPUT -i lo -j ACCEPT
```

####Create KeyTable file
```
nano /etc/opendkim/KeyTable
```

Your file should look like this:

```
dkim._domainkey.archimedes.ptechinnovations.com archimedes.ptechinnovations.com:dkim:/etc/postfix/dkim.key
```

####Create SigningTable file
```
nano /etc/opendkim/SigningTable
```

Your file should look like this:

```
archimedes.ptechinnovations.com dkim._domainkey.archimedes.ptechinnovations.com
```

#### Restart Postfix and opendkim

```
service opendkim restart
service postfix restart
```
## Generate DKIM key
Now you can generate a private key for signing outgoing mail. Note that in the following command, "dkim" is the value given to Selector in `/etc/opendkim.conf`. This can be any simple string, provided you are consistent about replacing "dkim" with your desired value everywhere in this recipe. Run the following command to generate the key and associated materials in the form of two files, `dkim.private` and `dkim.txt`. The former is the RSA private key, while the latter contains the entry the you will have to place into your DNS records.The first command is `cd` because you should be running from your home. 

```
cd
opendkim-genkey -t -s dkim -d example.com
```

Move the key into place, but don't forget to take a copy and keep that copy backed up somewhere safe:

```
mv dkim.private /etc/postfix/dkim.key
chown opendkim:opendkim /etc/postfix/dkim.key
```

You'll need to restart Postfix and OpenDKIM services to pick up the configuration changes so that outgoing mail is signed using DKIM:

```
service opendkim start
service postfix restart
```

Using your own method. Copy your `dkim.txt` from your server to your localhost. We're going to need it in our next step.

**Next**: proceed to [Configure Server DNS Records](server-dns-setup.md).