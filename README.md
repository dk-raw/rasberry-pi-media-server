# Rasberry Pi Automated Media Server
OMV 7, Portainer, Plex, Prowlarr, Radarr, Sonarr, Bazarr automated media server on Rasberry Pi 5

## Unifi settings (optional)
It's best practise to isolate the Rasberry Pi in your network, since it's torrenting all the time, for security. I have a Ubiquity Dream Machine running Unifi Network 7.2 and this is what I did to balance functionality and security
1. create a standard VLAN with no content filtering and the subnet of your choice

_Since Unifi allows all inter-VLAN traffic by default, certain firewall rules are required isolate that new network._

2. create a `LAN In` rule to `Drop` everything but `TCP` (select TCP under IPv4 Protocol and check `Match all protocols except for this` from Source the new VLAN, to Destination your Home network

3. repeat this but this time set as Source your Home netwrok and destination the isolated VLAN.

_These rules block all inter-VLAN traffic except for TCP/IP, so we will be able to ssh and access all the web servers running on the Pi (Plex, OMV etc.)_

Now, If you have IDS and IPS enabled and system sensitivity set to High, or blocked P2P Internet traffic, you need to make an exception for that specific Pi or the whole network we just created for it.

4. Under Logs>Threats, find all the unique signatured threat messages comming from the Pi as it's torrenting and click Allow This Threat Signature.
5. In the firewall settings, scroll down to Signature Suppression and you will see 6 unique entries. Edit each one of them so that direction is `Both`, mode is `Network` and network is the name of the isolated VLAN we created.

_If you have allowed and edited all 6, the list should look something like this:_
![image](https://github.com/dk-raw/rasberry-pi-media-server/assets/68110106/c1fa16af-9630-4b79-9e39-5104c957f361)

Now we have allowed torrenting on that VLAN, without unifi blocking it and sending threat notifications. We can then use Unifi's built in zero configuration VPN so that our media server is accessible anywhere inthe world, securely.

6. Go to Teleport & VPN, enable Teleport and generate a new link. On a laptop, phone, tablet that you will be travelling with download the WiFiman app and paste that link.

_Now that device is registered and once you are outside your network, maybe at a hotel or on an airplane, open the app and connect to the VPN. Since by default the subnet that teleport uses can talk to all our networks, no furhter firewall rules are required_
