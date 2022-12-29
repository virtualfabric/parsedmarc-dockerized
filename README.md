# parsedmarc-dockerized

Note: The inscructions here assume you are using a modern Debian derivative and that your are logged in as `root`. You may need to adapt these instructions for your distro/environment.


## Setup:

1. Set up your Docker environment:
You'll need at least 1.5GB RAM (2GB is recommended) and at least 25GB disk space. Your environment will also need access to Cloudflare's DNS service to function correctly.
```
apt update
apt -y upgrade
apt -y install vim git
reboot
```

```
curl -sSL https://get.docker.com/ | CHANNEL=stable sh
systemctl enable docker.service
systemctl start docker.service
curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64 > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```


2. Clone this repo:
```
git clone https://github.com/virtualfabric/parsedmarc-dockerized.git /opt/parsedmarc-dockerized/
```


3. Next we change the `parsedmarc` config (see [docs](https://domainaware.github.io/parsedmarc/#configuration-file). In most cases, you should only need to adjust the `[imap]` section to match your mail server config. It is recommended that you use an application password if your IMAP server supports it.
```
vim data/conf/parsedmarc/config.ini
```


4. Now change the environment file containing your geoipupdate settings from your [MaxMind account](https://www.maxmind.com/en/account/), to allow the container to pull the databases. For update cycles of the databases, please see [here](https://support.maxmind.com/geoip-faq/geoip2-and-geoip-legacy-database-updates/how-often-are-the-geoip2-and-geoip-legacy-databases-updated/).
```
vim geoipupdate.env
```


5. Finally, we start up the stack and wait:
```
docker-compose up -d
```


### What happens now?

1. First, containers of the stack are created and started. This might take a while, as several containers have dependencies on others being in a healthy state (meaning that its service must be fully started).
2. During the startup of the `parsedmarc-init` container, all required steps and preparations are being taken care of - like generating a self-signed certificate for the included `nginx` webserver.
3. Once the Kibana container - where you can view the dashboards - is started up, the corresponding parsedmarc dashboards are automatically imported into Kibana by the `parsedmarc-init` container.
4. After some while, when everything is up and running, you can then access Kibana and its dashboards under the shipped reverse proxy at `https://HOST_IP:9999`. (Make sure to use HTTPS!)


## Credits

Built with awesome [parsedmarc](https://github.com/domainaware/checkdmarc), [Elasticsearch and Kibana](https://www.elastic.co/), [nginx](https://nginx.org), [Docker](https://docker.com) and [MaxMind GeoIP](https://dev.maxmind.com/geoip/geoip2/geolite2/).