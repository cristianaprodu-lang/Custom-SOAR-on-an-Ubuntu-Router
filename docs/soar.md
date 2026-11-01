# Step 1: Detecting attacks
> To establish the foundation of the SOAR engine, I needed a reliable method to detect malicious traffic entering the network. I utilized **Suricata** as the primary Intrusion Detection System (IDS), configured in an out-of-band architecture. This design choice ensures that the IDS passively monitors the traffic on the WAN-facing interface without introducing routing latency or acting as a bottleneck.

Suricata is configured to export its findings in a highly structured JSON format to `/var/log/suricata/eve.json`. Instead of relying on external SIEM forwarders, I developed a custom Python daemon to ingest these logs in real-time. 

Upon execution, the script seeks to the end of the log file to ignore historical data and only process live packets. It continuously reads new lines, parses the JSON payload, and filters out network noise (such as Layer 2 keepalives or STP packets) by verifying the presence of a valid source IP address.

Below is the core detection logic implemented in the engine:
<img width="1173" height="634" alt="image" src="https://github.com/user-attachments/assets/2c5152ee-d8f3-466e-b0b5-7854207f2aeb" />

I ran a few SSH connections from the Windows Machine to the Ubuntu Router. As you can see, detection works as intended, showing me the source IP and the signature. I will improve the script later on, but for now I wanted to showcase the basic functionality. 
<img width="1141" height="77" alt="image" src="https://github.com/user-attachments/assets/ef473e87-044f-4a0b-a516-5a22b9f76881" />

