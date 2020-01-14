# Esp8266Wpa2Handshake
When learning about Wi-Fi hacking, WPA2 cracking is the most common example to teach.

To crack the password of a WPA2 network, we must have four things:
1) A network card to listen on so that we can intercept a handshake
2) A handshake to capture so that we can create guesses and compare them to this value
3) A list of passwords to guess so that we can hash them and compare them to the captured handshake value
4) A program to convert our list of possible passwords into a PSK, and compare it to the value captured in the handshake

All of these can be done on a Raspberry Pi zero W, which brings the cost of this down to about $10.
The final problem is to create a Wi-Fi network to reliably and legally practice WPA2 cracking that is cheap.

To solve this, @spacehuhn and I have tested a proof of concept to replay packets from a WPA2 handshake from a single device.
This makes it look like one device is joining a Wi-Fi network, but all the traffic comes from a single esp8266.

To do this, we captured the essential parts of a handshake:
* The 4 message EAPOL key (4 way handshake)
* Beacon frames containing the ESSID (network name) of the network the device is joining

The beacon frames are needed to convert our password guesses into a hash to compare to the captured handshake.

After capturing the beacon frames and EAPOL exchange, we created a sketch to play these packets every second.

Observed in Wireshark, we should have all the eapol traffic needed to crack the WPA2 password using aircrack-ng.

You can observe this with the Wireshark filter: 
wlan.ssid == "test" || eapol

To test, upload this sketch to an D1 Mini after changing the Wi-Fi channel to the one you want to monitor.  
Record Wi-Fi traffic on that channel in Wireshark with the display filter above.  
Save the traffic as a .PCAP file, run aircrack-ng with a wordlist to crack (-w argument).   
If the wordlist contains the password (hint: german for entrance) aircrack-ng should crack the password

![alt text](https://pbs.twimg.com/media/EOMEzfbUUAAXM1M?format=png&name=medium "Cracked!")




