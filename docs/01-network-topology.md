# The topology

1. the LAN with a windows 10 machine, which will act as the victim
2. DMZ (demilitarized zone) with a Metasploitable Server, also a victim
3. WAN, where the attacks come from.
<img width="650" height="621" alt="image" src="https://github.com/user-attachments/assets/88a74e73-8c61-4015-9c69-f35c651d51d2" />

## Making the Ubuntu machine a router
I configured the Ubuntu machine with 3 network cards. Interface enp0s3 to WAN (address via DHCPv4), enp0s8 to LAN (static routing) and enp0s9 to DMZ (static routing).
``` bash
nano /etc/netplan/01-network-manager-all.yaml
```
<img width="940" height="620" alt="image" src="https://github.com/user-attachments/assets/de41542b-c189-4ccb-b41d-08deee2847e6" />

Saving the configuration:
``` bash
sudo netplan apply
```
Ping works, we are connected to the internet now.
<img width="940" height="323" alt="image" src="https://github.com/user-attachments/assets/0b79cd96-a9ca-428c-8115-60d56dfeb372" />

Activating the routing:
``` bash
nano /etc/sysclt.conf 
```
<img width="940" height="618" alt="image" src="https://github.com/user-attachments/assets/db15b77a-0933-4ea7-aec5-572f883d8d86" />

## Configuring the IP address on the Windows endpoint using 2 methods:
1. Control Panel > Network and Internet > Network and Sharing Center > Ethernet > Properties > TCP/IPv4 > Properties
2. Start > ncpa.cpl
<img width="476" height="543" alt="image" src="https://github.com/user-attachments/assets/f3c67d52-7d55-4d32-89b7-2ad9a0bc9037" />

Let's check the connectivity:

1. The ping towards the router works
<img width="722" height="492" alt="image" src="https://github.com/user-attachments/assets/bcbfc8ee-4891-42c6-89ee-641d2e2cf5c0" />

2. We don't have internet yet, because I haven't activated DNS on the router.
<img width="734" height="586" alt="image" src="https://github.com/user-attachments/assets/d74cea05-c647-47e7-b4f9-65fb5c44eb5e" />

DNS Activation: ``` sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE ```

<img width="569" height="406" alt="image" src="https://github.com/user-attachments/assets/4ea46408-f725-4b92-a60f-fe940baf5e81" />
We have internet now!

## DMZ-Server configuration //todo
