# portmap
A setup that configures dnsmasq and nginx to map friendly domain names (like <http://ipython.p>) to local ports (like <http://localhost:8888>).

# Setup
Setup still isn't very friendly (comments, suggestions or pull requests to improve this are welcome). At a high level portmap consists of 3 components: a static dnsmasq config, a set of nginx configs (one per port-name mapping), and a script to manage the port configurations.

## dnsmasq
You can just install dnsmasq as normal and use the provided configuration. I use homebrew and just did `brew install dnsmasq`, copied [dnsmasq.conf](dnsmasq.conf) to `/usr/local/etc/dnsmasq.conf`, and followed the formula's instructions on how to get dnsamsq to automatically start on boot (`sudo brew services start dnsmasq`). The configuration itself has only one uncommened line: `address=/.p/127.0.0.1`. This causes dnsamsq to map any domain ending in ".p" to the local machine.

In addition, you need to configure the system to use the local dnsmasq as a DNS server. In OS X, go into System Preferences -> Network -> Advanced -> DNS and just add 127.0.0.1 as one of the DNS servers.

## nginx
Install nginx with `brew install nginx`. Copy `dns_proxy.conf` to `/usr/local/etc/nginx/`.

The nginx config is divided into two parts: `dns_proxy.conf` has some high-level configuration and then includes all the port configurations. Start nginx with this config, `nginx -c /usr/local/etc/nginx/dns_proxy.conf`. Port configurations will go in `dns-ports/` relative to this file.

## portmap
Finally, `portmap` manages the mappings in `dns-ports`. When you add a mapping with the script, it modifies a simple text file called `dns-services.txt`, then generates files like `dns-ports/ipython.conf` based on the mustache template `service.conf.mustache`, then reloads nginx. To begin, copy the provided [dns-services.txt](dns-services.txt) over to `/usr/local/etc/nginx/`.
