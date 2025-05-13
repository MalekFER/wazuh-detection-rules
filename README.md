  # Wazuh Detection-as-Code: SSH Brute-force Detection

## Overview

This project demonstrates how to implement **Detection-as-Code (DaC)** using **Wazuh** to detect brute-force SSH login attempts. The goal is to build a custom rule, simulate an attack, validate detection logic, and version-control it using Git.

## Objectives

- Detect SSH brute-force attempts using Wazuh
- Simulate real-world attack logs (with Hydra)
- Write and test a custom Wazuh rule
- Validate detection using `ossec-logtest`
- Store and document the rule in a Git-based workflow

## Rule Description

We created a custom rule to detect SSH login failures, which are often a sign of brute-force attacks.

### 📄 Sample Log:
sshd[10782]: Failed password for root from 192.168.75.160 port 58372 ssh2


### 🛡️ Detection Rule (inside `rules/local_rules.xml`):
```xml
<group name="sshd,authentication_failed,">
  <rule id="100001" level="10">
    <decoded_as>json</decoded_as>
    <field name="system">sshd</field>
    <field name="event">Failed password</field>
    <description>SSH Brute-force attempt detected</description>
  </rule>
</group>
```


📌 Project Steps
1. Simulate a Brute-Force Attack
Use Hydra to perform SSH brute-force attempts from an attacker machine.

      hydra -l root -P passwords.txt ssh://<victim-ip> -t 4
This generates multiple failed SSH login logs on the target machine.

3. Check the Generated Logs
On the target (Wazuh) machine, view the SSH logs:

sudo journalctl -u ssh
# or
sudo cat /var/log/auth.log
Copy one or more of the log entries showing Failed password.

3. Create a Custom Rule
Edit or create the file:

sudo nano /var/ossec/etc/rules/local_rules.xml
Add a custom Wazuh rule to detect brute-force attempts:

<group name="ssh,authentication_failed,">
  <rule id="100001" level="10">
    <if_sid>5710</if_sid>
    <field name="srcip">192.168.75.160</field>
    <description>SSH Brute-force attempt detected</description>
  </rule>
</group>


4. Test the Rule with ossec-logtest
Run the Wazuh test tool:

      /var/ossec/bin/ossec-logtest
Paste in one of the SSH failed login logs:

      2025-05-10T12:24:41.809478+01:00 wazuh sshd[10782]: Failed password for root from 192.168.75.160 port 58372 ssh2
If your rule works, you'll see it match with the rule ID and alert level.



5. Restart Wazuh Manager
    sudo systemctl restart wazuh-manager
This ensures the new rule is loaded.



6. Verify Detection in the Wazuh Dashboard
Open the Wazuh web interface.

Go to Security Events or Alerts.

Confirm that the brute-force detection alert appears.





*********Technologies Used**************

Wazuh (SIEM/EDR platform)

Hydra (for brute-force testing)

Git & GitHub (for DaC workflow)

Linux (Ubuntu 22.04 environment)
