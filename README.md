# Rasberry Pi Automated Media Server
OMV 7, Portainer, Plex, Prowlarr, Radarr, Sonarr, Bazarr automated media server on Rasberry Pi 5

## Unifi settings (optional)
It's best practise to isolate the media server in your network, since it's torrenting all the time, for security. This is what I did to balance functionality and security on the Unifi Network 8.2
1. create a standard VLAN with no content filtering and the subnet of your choice. Make sure to assign the media server with an IP from this subnet, preferably a static one.

_Since Unifi allows all inter-VLAN traffic by default, certain firewall rules are required to isolate that new network._

2. create a `LAN In` rule to `Drop` everything but `TCP` (select TCP under IPv4 Protocol and check `Match all protocols except for this` from `Source` the new VLAN, to `Destination` your Home network

3. repeat this but this time set as Source your Home netwrok and destination the isolated VLAN.

_These 2 rules block all inter-VLAN traffic except for TCP/IP, so you will be able to ssh and access all the web servers running on the Pi (Plex, OMV etc.). If you have others VLANs you need to create these 2 rules for each one respectively so obviously this method doesn't scale. Consider creating a global rule to drop all inter-VLAN traffic and then 2 individual rules for allowing TCP/IP from the media server VLAN to your Home network. Make sure said rules are higher in the hierarchy so they override the global drop rule_ 

Now, If you have IDS and IPS enabled and system sensitivity set to High, or at least blocked P2P Internet traffic, you need to make an exception for the media server or the whole network it lives at.

4. Under Logs>Security Detections, find all the unique signatured threat messages comming from the media server as it's torrenting and click Allow This Threat Signature.
5. In the firewall settings, go to Signature Suppression and you should see 8 unique entries. Edit each one of them so that direction is `Both`, mode is `Network` and network is the name of the isolated media server VLAN

_If you have allowed and edited all 8, the list should look something like this:_
![screenshot](https://github.com/dk-raw/rasberry-pi-media-server/assets/68110106/d5822027-9188-4244-807d-8975b8181df0)
_Ugandan Nodes is the name of the media server VLAN_


Now we have allowed torrenting on that VLAN, without unifi blocking it and sending threat notifications. We can then use Unifi's built in zero configuration VPN so that the media server is accessible anywhere in the world, securely.

6. Go to Teleport & VPN, enable Teleport and generate a new link. On a laptop, phone, tablet that you will be travelling with download the WiFiman app and paste that link.

_Now that device is registered and once you are outside your network, open the app and connect to the VPN. Since by default the subnet that teleport uses can talk to all the VLANs, no furhter firewall rules are required_
