#Configure Server DNS Records
> Reference: ExRatione's [Setting Up SPF and DKIM for an Ubuntu 14.04 Mail Server](https://www.exratione.com/2014/07/setting-up-spf-and-dkim-for-an-ubuntu-1404-mail-server)
 
This step will be different based on your vps vendor. It's impossible to create this tutorial for every host provider. This is tutorial is created using [Linode](https://www.linode.com) as a vps server and [Stablehost](https://www.stablehost.com) as a Domain provider. 

## Enable Records
##### Create A record point your mail address to your vps ipv4 address. 
Name:`archimedes.ptechinnovations.com`  
Address: `203.0.113.10`

##### Create AAAA record to point your mail address to your ipv6 address
Name:`archimedes.ptechinnovations.com`  
IPv6 Address:`2600:3c01::a123:b456:c789:d012 `

#### Add SPF Record   
Create TXT Record   
Name:`archimedes.ptechinnovations.com`  
TXT Data:`v=spf1 a:archimedes.ptechinnovations.com -all`

#### Create Reverse Lookup for vps
* Configure Reverse DNS for your server. There is no instructions for this as this is specific to your vps provider

#### Create TXT record for DKIM key Lookups  
The TXT data fields come from your dkim.txt file from the previous tutorial. 

When setting this up you should omit the `"t=y;"` portion of the value. It denotes that this is a test entry, and should not be included. So the value looks like this:

* First Record:  
Name: `dkim._domainkey.archimedes.ptechinnovations.com`  
TXT Data: 

```
v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBifQKBgQDMVBMuf0aEqRqcm6r95fe4wNOMtAbogasdfkM9IAEpmGMptVuVrXYj51qu2nKfNA2McQ9yaR4eezKnTKi5asdfl9QkXsOaYJQeQksoKHWjOg4Ypf36lMoRchN7XyZwLOg88Mk7+KsujKTOnxqEEnM1r0eKHUNfo5w8lL7anS/lavLTn0w8Yvc8wIDAQAB
```

* Second Record:   
Name: `_domainkey.archimedes.ptechinnovations.com`  
TXT Data: 

```
v=DKIM1; k=rsa; p=MIGfMA0GCSqdGSIb3DQEBAQUAA4GNADCBiQKBgQDMVBMuff0aEqRqcm6r95fe4dwNOMtAboglkM9IAEpmGMptVuVrXYj51qu2nKfNA2McQ9yaR4aefezKnTKi5fl9QkXOaYJQeQksoKHWjOg4Ypf36lMoRchN7XyZhwLOg88Mk7+KsujKTOnxqEEnM1r0eKHUNo5w8lL7nS/lavLTn0aw8Yvc8wIDAQAB
```