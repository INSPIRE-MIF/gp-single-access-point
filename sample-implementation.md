# **Description of a testbed implementation of collective WMS service**

##Introduction
This document describes the process of installation and configuration of modified MapServer supporting integrating WMS services. 
Before reading this document, it is advisable to read the document Guidance for the integration of dispersed WMS sources first to understand the theoretical background of collective services.
The key MapServer functionality for the WMS server integration is WMS Client. The functionality allows MapServer to transparently fetch remote layers over WMS, basically acting like a client, and combine them with other layers to generate the final map. You can find more about the WMS client configuration in MapServer documentation (https://mapserver.org/ogc/wms_client.html).

##Changes to MapServer
This version of MapServer has been built on modified version 7.0.7. Following changes/optimisations have been made in the code:
1.	Modified spatial extend of the WMS service. The standard version of MapServer allows defining spatial extend of WMS service as a bounding box using wms_latlonboundingbox parameter. Modified version allows setting spatial extent of local WMS service using introduced parameter gs_bounds as polygons provided in Well-known text (WKT) format. MapServer will request the local WMS service only when the map view overlaps that polygon, thus reducing network traffic.
2.	Redirection mechanism. If a request covers the spatial extend of only one local service, the collective service does not forward the request to the local service. Still, it returns to the client application proper URI pointing directly to a local service. This is done by utilising properties of Hypertext Transfer Protocol (HTTP). Collective service sends a response URL to the local service with the 302 status code. To achieve this additional parameter gs_sendUrl has been introduced.
3.	Cookies support. Some local servers may require sending additional information stored in cookies. Modified MapServer allows adding cookies to requests. Cookies are stored in a separate file but generated independently from the MapServer app.
4.	Grouping of layers. Request to different layers of the same WMS local service is aggregated. Instead of many requests, one separately for each layer, one request to a WMS local service is sent.
Other minor modifications are focused on improving the efficiency of some MapServer functionalities related to services’ integration, e.g. turning off of not used modules.
Modified MapServer Map files containing services’ configuration cannot be used on the standard version of MapServer. However, standard MapServer configuration files work in the modified version.

##Installation
The installation and configuration described in this document have been tested on Debian 9. Installation and configuration on other operating systems may require changes related to versions of installed libraries and further alteration of MapServer code.
The document assumes that the user with root privileges using the console carries the installation process with the installation process.

#Apache installation
``
sudo apt-get update
sudo apt-get upgrade
apt-get install apache2 apache2-bin apache2-utils libapache2-mod-fcgid
a2enmod cgi fcgid
Check whether the Apache server is working by putting its IP address in the web browser. If everything is ok, the browser should display the Apache default website.

#MapServer installation
Download the file mapserver-7.0.7.zip from GitHub and copy it to location /usr/src/
```
wget 
cp mapserver-7.0.7.zip /usr/src/
```
Unzip the file
```
apt install unzip
unzip mapserver-7.0.7.zip
```
Install libraries required and recommended to run MapServer
```
apt install gdal-bin libgdal-dev libproj-dev proj-bin  libpng-dev freetype2-demos zlib1g-dev libgeos-dev libgif-dev cmake libfcgi-dev libpcre3 libpcre3-dev libcairo2-dev libharfbuzz-dev libfribidi-dev libcurl4-gnutls-dev curl php php-common
```
Copy file mapserv file from /usr/src/mapserv-7.0.7/build/ to /usr/lib/cgi-bin/
```
cp /usr/src/mapserv-7.0.7/build/mapserv to /usr/lib/cgi-bin/
```
Make sure the mapserv file has executable permissions
```
cd /usr/lib/cgi-bin/
chmod 755 mapserv
```
check whetehr mapserver works
```
./mapserv -v
```

Building application (optional)
If you want to compile MapServer code, you may optionally install appropriate libraries
apt install build-essential protobuf-c-compiler protobuf-compiler libprotobuf-c-dev
More about compiling MapServer you can find in the documentation (https://www.mapserver.org/installation/unix.html).
In order to enable key wms client feature, you will need to pass the WITH_CLIENT_WMS option to the configure script e.g.:
cmake -DCMAKE_INSTALL_PREFIX=/opt -MAKE_PREFIX_PATH=/usr/local:/opt:/lib:/usr/local/lib:/usr/lib - WITH_CLIENT_WMS=ON ../ >../configure.out.txt

Collective service configuration
This chapter describes how to set up a copy of sample service in your environment.
Download config files and place them in appropriate locations.
cp.map and capabilities.xml files can be placed in a location of your choice, e.g. /srv/cp/. cp.map file contains service configuration including URL of local services. More information about WMS service configuration in MapServer is available in the documentation (https://mapserver.org/ogc/wms_server.html). capabilities.xml file contains GetCapabilities response of collective WMS service. When a user sends GetCapabilities request the default MapServer response is replaced by the one in capabilities.xml file. It is important to point out that it is not required to replace default GetCapabilities response. However, modified GetCapabilities response is shorter, simplified and more clear and thus more readable for a user.
cd /srv
mkdir cp
cd cp
wget
wget
Create error.txt file for MapServer logs
touch errot.txt
chmod 666 error.txt

Place wmssampleintegration and wms_info.php files in folder /usr/lib/cgi-bin/ and give them required privileges. File wmssampleintegration redirects incoming user request. There are three redirections in the sample implementation:
•	GetCapabilities request is redirected to srv/cp/capabilities.xml
•	request without parameters is redirected to wms_info.php file
•	request with parameters other than GetCapabilities is redirected to MapServer
wms_info.php file contains website with information about the service. This redirection is optional and doesn’t have to be implemented. However, well designed information website improves the usability of the service.

cd  /usr/lib/cgi-bin/
wget
wget
chmod 755 wmssampleintegration
chmod 755 wms_php.info

Set a correct URL for the service. Use the text editor of your choice, e.g. nano.
nano /srv/cp/cp.map
Edit line with parameter OWS_ONLINERESOURCE and change its value to an appropriate endpoint URL.
Make also similar changes in the capabilities.xml file. Endpoint has to be alerted in several locations in this file.
nano /srv/cp/capabilities.xml

Verify whether service works http://<server_url>/cgi-bin/wmssampleintegration?REQUEST=GetCapabilities&SERVICE=WMS should return the capabilities file. Depending on your specific configuration of your server, please remember to replace <server_url> with appropriate IP address or domain name
http://<server_url>//cgi-bin/wmssampleintegration?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&BBOX=505599.190755055286,683480.588842289988,506706.9429105632007,684719.5045823869295&CRS=EPSG:2180&WIDTH=1250&HEIGHT=1117&LAYERS=cadastral_parcels&STYLES=&FORMAT=image/png&DPI=96&MAP_RESOLUTION=96&FORMAT_OPTIONS=dpi:96&TRANSPARENT=TRUE  should return the map with cadastral parcels file
 