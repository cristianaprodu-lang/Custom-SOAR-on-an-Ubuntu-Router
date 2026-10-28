## Description:
This chapter covers the deployment and tuning of Suricata as an out of band IDS on the Ubuntu router. The goal is to analyze L4-L7 traffic across the LAN and DMZ interfaces with zero latency impact, exposing alerts in JSON format for the SOAR engine.

### Installation
Add OISF repository and install:

```bash
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt update
sudo apt install suricata -y
suricata -V //to verify the installation 
```
making a backup in case I break anything and i need to restart
```bash
sudo cp /etc/suricata/suricata.yaml /etc/suricata/suricata.yaml.bak
```

Configuring the .yaml file: https://docs.suricata.io/en/latest/configuration/suricata-yaml.html

<img width="733" height="482" alt="image" src="https://github.com/user-attachments/assets/b113280e-4372-4e46-bf3a-a2196402b68d" />
<img width="727" height="482" alt="image" src="https://github.com/user-attachments/assets/1860b8c9-7c1b-4497-a364-5b9b9a30d829" />

I modified suricata.yaml to only include the addresses in my home-net. Restricting HOME_NET strictly to the LAN and DMZ subnets prevents false negatives from default IP ranges and ensures high-fidelity, zero-noise alerts for the SOAR engine.

We bound Suricata to the LAN (enp0s8) and DMZ (enp0s9) interfaces so the IDS can actually see the traffic going towards our target machines. We used the af-packet mode because it's a fast capture method native to Linux, allowing Suricata to analyze packets without slowing down the router.
<img width="893" height="492" alt="image" src="https://github.com/user-attachments/assets/5e01e67e-2ed7-4377-8052-bf63939d4cb1" />

After saving the configuration, I ran a syntax check to validate the YAML file:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v
```
The test was successful, confirming our network configuration, but returned a standard warning: No rule files match the pattern /var/lib/suricata/rules/suricata.rules. This happens because a fresh Suricata installation does not include an active rule dictionary by default.

To resolve this and bring the IDS to life, I used the built-in update tool to download the latest Emerging Threats Open ruleset:

```bash
sudo suricata-update
```
This command fetches the newest attack signatures from the internet, optimizes them, and automatically generates the required suricata.rules file that our detection engine relies on.

### Starting the IDS Service
With the rule dictionary successfully downloaded, the Suricata engine was fully armed. I enabled the Suricata systemd service to start automatically on system boot and launched it in the background:

```bash
sudo systemctl enable suricata
sudo systemctl restart suricata
sudo systemctl status suricata
```

The IDS is now monitoring the LAN and DMZ interfaces and writing structured alerts to /var/log/suricata/eve.json, ready to be ingested by the custom SOAR engine.

In order to test that Suricata is working, I downlaoded a simulated attack payload from testmyids.com
```bash
curl http://testmyids.com
```
```bash
sudo tail -n 20 /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```
<img width="735" height="307" alt="image" src="https://github.com/user-attachments/assets/9742e21f-894d-439f-9600-2b921f9df542" />

Everything works fine, it's time to move on to making the SOAR.
