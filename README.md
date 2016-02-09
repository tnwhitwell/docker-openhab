# docker-openhab

Extends https://hub.docker.com/r/tdeckers/openhab/ with yowsup2.

Docker image for Openhab (1.8.1). Included is JRE 1.8.45.

# Official demo included

If you do not have a openHAB configuration yet, you can start this Docker without one. The official openHAB demo will be started. 

# Running

* The image exposes openHAB ports 18080 (jetty), 18443 (jetty ssl), 15555 (console) and 19001 (supervisord).
* It expects you to map a configurations directory on the host to /etc/openhab. This allows you to inject your openhab configuration into the container (see example below).
* To enable specific plugins, add a file with name addons.cfg in the configuration directory which lists all addons you want to add.
* Auto-detect of devices with UPnP (i.e sonos binding): Run container with --net=host option. This will use the network interface of the host instead of creating a separate one for the container. In practice it will map 1:1 all ports on the container to the host and enable the container to receive multicast UDP messages. https://github.com/docker/docker/issues/3043


Example content for addons.cfg:
```
org.openhab.action.mail
org.openhab.binding.astro
org.openhab.binding.exec
org.openhab.binding.http
org.openhab.binding.mystromecopower
org.openhab.binding.netatmo
org.openhab.binding.ntp
org.openhab.binding.sonos
org.openhab.persistence.exec
org.openhab.persistence.logging
org.openhab.persistence.rrd4j
```

* The openHAB process is managed using supervisord.  You can manage the process (and view logs) by exposing port 19001. From there it is possible to switch between NORMAL and DEBUG versions of OpenHAB runtime.
* The container supports starting without network (--net="none"), and adding network interfaces using pipework.
* You can add a timezone file in the configurations directory, which will be placed in /etc/timezone. Default: UTC

Example content for timezone:
```
Europe/Brussels
```

Example: run command (with your openHAB config)
```
docker run -d -p 18080:18080 -v /tmp/configuration:/etc/openhab/ lakermann/docker-openhab
```

Example: run command (with your openHAB config and images)
```
docker run -d -p 18080:18080 -v /tmp/configuration:/etc/openhab/ -v /tmp/images:/opt/openhab/webapps/images lakermann/docker-openhab
```

Example: Map configuration and logging directory as well as allow access to Supervisor:
```
docker run -d -p 18080:18080 -p 19001:19001 -v /tmp/configurations/:/etc/openhab -v /tmp/logs:/opt/openhab/logs lakermann/docker-openhab
```

Example: run command (with Demo)
```
docker run -d -p 18080:18080 lakermann/docker-openhab
```

Start the Demo with: 
```
http://[IP-of-Docker-Host]:18080/openhab.app?sitemap=demo
```
Access Supervisor with: 
```
http://[IP-of-Docker-Host]:19001
```


# HABmin

HABmin is not included in this deployment.  However you can easily add is as follows:
```
docker run -d -p 18080:18080 -v /<your_location>/webapps/habmin:/opt/openhab/webapps/habmin -v /<your_location>/openhab/config:/etc/openhab -v /<your_location>/openhab/addons-available/habmin:/opt/openhab/addons-available/habmin lakermann/docker-openhab
```

Then add these lines to addon.cfg
```
habmin/org.openhab.binding.zwave
habmin/org.openhab.io.habmin
```

# Contributors
* maddingo
* scottt732
* TimWeyand
* dprus
* tdeckers
* wetware
* carlossg
* lakermann
