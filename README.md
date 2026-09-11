# IDS-IPS-system
IDS/IPS with Endpoint Monitoring Using Suricata, Wazuh, Fail2Ban, and UFW

Project Overview

This project demonstrates the design and implementation of a layered Intrusion Detection and Intrusion Prevention System (IDS/IPS) with centralized endpoint monitoring.

The solution combines network-based detection, host-based monitoring, event correlation, and automated response to provide security visibility and protection across Linux endpoints.

Technologies Used

- Suricata (Network IDS)
- Wazuh (Endpoint Security Monitoring Platform)
- Fail2Ban (Intrusion Prevention System)
- UFW (Firewall Enforcement)

Objective

Detect suspicious activities, collect and correlate security events, generate alerts, and automatically respond to threats using predefined security policies.

Architecture

Network Traffic
      ↓
Suricata
      ↓
Wazuh
      ↓
Fail2Ban
      ↓
UFW
      ↓
Threat Containment

Suricata

Responsibilities:

- Monitors network traffic
- Inspects packets in real time
- Detects suspicious activity using rules
- Generates structured security events

Command

sudo apt update
sudo apt install -y suricata

Verify Installation

Command

suricata --build-info

Enable and Start Service

Command

sudo systemctl enable suricata
sudo systemctl start suricata

Check Service Status

Command

sudo systemctl status suricata

Identify Network Interface

Command

ip link

Configure Suricata

Command

sudo nano /etc/suricata/suricata.yaml

Example Configuration

af-packet:
  - interface: eth0

Restart Service

Command

sudo systemctl restart suricata

Monitor Events

Command

sudo tail -f /var/log/suricata/eve.json

Wazuh

Responsibilities:

- Collects system logs
- Monitors endpoint activity
- Correlates security events
- Provides centralized visibility through the dashboard

Install Prerequisites

Command

sudo apt install -y curl apt-transport-https unzip lsb-release gnupg

Download Installer

Command

curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
chmod +x wazuh-install.sh

Deploy Wazuh

Command

sudo ./wazuh-install.sh -a

Verify Services

Command

sudo systemctl status wazuh-manager wazuh-indexer filebeat wazuh-dashboard --no-pager

Verify Agent

Command

sudo systemctl status wazuh-agent

Endpoint Monitoring

Wazuh monitors:

- Authentication events
- User activity
- System logs
- File integrity changes
- Policy violations
- Privilege escalation attempts
- Malware indicators
- Configuration modifications

Fail2Ban

Responsibilities:

- Monitors system logs
- Detects repeated malicious behavior
- Applies security policies
- Triggers automated blocking actions

Install Fail2Ban

Command

sudo apt install -y fail2ban

Enable Service

Command

sudo systemctl enable fail2ban
sudo systemctl start fail2ban

Create Local Configuration

Command

sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

Edit Configuration

Command

sudo nano /etc/fail2ban/jail.local

Example Configuration

[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
findtime = 300
maxretry = 10
bantime = 600
backend = systemd

Restart Service

Command

sudo systemctl restart fail2ban

Verify Status

Command

sudo fail2ban-client status
sudo fail2ban-client status sshd

UFW Firewall

Responsibilities:

- Enforces firewall rules
- Restricts unauthorized access
- Implements Fail2Ban actions

Install UFW

Command

sudo apt install -y ufw

Allow SSH

Command

sudo ufw allow ssh

Enable Firewall

Command

sudo ufw enable

Verify Firewall Status

Command

sudo ufw status

Wazuh Log Integration

Forward Suricata Events

Command

sudo nano /var/ossec/etc/ossec.conf

Add

<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>

Forward Fail2Ban Events

Add

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/fail2ban.log</location>
</localfile>

Restart Agent

Command

sudo systemctl restart wazuh-agent

Centralized Endpoint Monitoring

Each endpoint runs:

- Suricata
- Wazuh Agent
- Fail2Ban
- UFW

All endpoints report to a centralized Wazuh Manager.

Benefits:

- Centralized monitoring
- Event correlation
- Unified dashboard visibility
- Simplified incident response
- Improved operational efficiency

Automated Response

Wazuh Active Response can:

- Block malicious IP addresses
- Disable compromised accounts
- Terminate suspicious processes
- Isolate affected systems
- Trigger notifications

Security Policy Example

R1  Low-risk event detected → Log event
R2  Repeated suspicious activity → Generate alert
R3  Security threshold exceeded → Trigger automated block
R4  High-confidence threat detected → Contain and record incident

Troubleshooting

Check Manager Address

Command

grep "<address>" /var/ossec/etc/ossec.conf

Test Connectivity

Command

nc -zv <manager-ip> 1514
nc -zv <manager-ip> 1515

View Agent Logs

Command

sudo tail -50 /var/ossec/logs/ossec.log

Dashboard Configuration Issue

Following a platform upgrade, a dashboard API configuration issue was encountered and resolved by editing:

Command

sudo nano /usr/share/wazuh-dashboard/data/wazuh/config/wazuh.yml

Restart Service

Command

sudo systemctl restart wazuh-manager

Key Features

- Network intrusion detection with Suricata
- Endpoint monitoring with Wazuh
- Event correlation and centralized visibility
- Automated prevention using Fail2Ban
- Firewall enforcement using UFW
- Centralized monitoring across multiple endpoints
- Automated incident response through Wazuh Active Response

Conclusion

This project demonstrates a layered defense architecture that combines network monitoring, endpoint visibility, event correlation, and automated response.

By integrating Suricata, Wazuh, Fail2Ban, and UFW, the solution provides a practical IDS/IPS platform capable of detecting security events, monitoring endpoints, centralizing alerts, and enforcing defensive actions through automated response mechanisms.

Note: Sensitive information such as IP addresses, credentials, hostnames, enrollment keys, and environment-specific configurations have been anonymized or replaced with placeholders for security purposes.
