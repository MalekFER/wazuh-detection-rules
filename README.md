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


Steps Followed

✅ Created a Git-based project to manage detection rules.

🧪 Launched a brute-force attack using Hydra:

        hydra -l root -P passwords.txt ssh://<target-ip> -t 4

        

📂 Extracted failed login attempts from /var/log/auth.log.

✍️ Wrote a detection rule for Wazuh.

🧠 Validated it using:
            /var/ossec/bin/ossec-logtest
📤 Committed and pushed the project to GitHub.




*********Technologies Used**************

Wazuh (SIEM/EDR platform)

Hydra (for brute-force testing)

Git & GitHub (for DaC workflow)

Linux (Ubuntu 22.04 environment)
