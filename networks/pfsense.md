# PfSense Notes

## Dynamic DNS Configuration

For this, dynamic dns provider is needed. In this example, free service at [Duck DNS](https://www.duckdns.org/)is used.

**Duck DNS Configuration**
1. Register [here](https://www.duckdns.org/) for free
2. Create new domain with custom name (fqdn will be your-custom-name.duckdns.org)
3. Take note of your domain and token
4. Up in the menu bar, click "install"
5. Under "first step - choose a domain" select your newly created domain and under routers" click "pfSense"
6. There you will get needed instruction for the pfSense configuration. Two things you need: 
	* URL with your domain name and token will be generated for you  
		* Example: if domain was example.duckdns.org and token was 64c51dbf-26b5-462b-b021-e5591055789
		* URL: https://www.duckdns.org/update?domains=example&token=64c51dbf-26b5-462b-b021-e5591055789&ip=%IP%
	* Result message will be shown (OK)

**pfSense Configuration**
1. Go to Services > Dynamic DNS
2. Configure as follows:

```text
Service type: Custom
Interface to monitor: WAN
Interface to send update from: WAN
Update URL: your custom URL from step 6 in the Duck DNS configuration
Result Match: OK
Description: Something descriptive
```

3. Hit "Save" and you should be good to go

In case your pfSense sits behind DSL/Cable modem or other router, update won't be on public ip change per detection but once in a day as per "rc.dyndns.update" cron job exectuion. For this to be working in a more timely manner, we need to edit the cron job repetition interval from once in a day to every 5 minutes.

1. Install "Cron" package in pfSense by doing System > Package Manager > Available Packages > Search Cron and install it
2. Then under Services > Cron find "rc.dyndns.update" cron job, click "Edit" and change as below:
```text
Minute: */5
Hour: *
mday: *
month: *
wday: *
```

