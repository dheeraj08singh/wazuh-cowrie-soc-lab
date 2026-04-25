🚨 SOC Detection Lab using Wazuh + Cowrie Honeypot



📌 Overview



This project demonstrates how to build a basic \*\*Security Operations Center (SOC) lab\*\* using:



\* \*\*Cowrie Honeypot\*\* → to simulate real-world SSH attacks

\* \*\*Wazuh SIEM\*\* → to collect, analyze, and detect attacks



The goal of this project is to understand how security monitoring works in real environments by building a complete detection pipeline from scratch.



\---



🧠 Architecture



```

Attacker → Cowrie Honeypot → Log File → Wazuh Agent → Wazuh Manager → Alerts → Dashboard

```



\---



⚙️ Tools \& Technologies



\* Wazuh (SIEM)

\* Cowrie (SSH Honeypot)

\* Kali Linux

\* JSON Log Analysis



\---

🔍 Network Reconnaissance & Vulnerability Analysis

To enhance the SOC lab, additional analysis was performed using Nmap to identify exposed services and validate potential vulnerabilities.

🎯 Objective

Identify externally exposed services
Validate attack surface detected in the SOC lab
Correlate vulnerabilities with SIEM alerts

🧪 Scans Performed

1️⃣ Localhost Scan - nmap 127.0.0.1

Findings:

Port 443 (HTTPS)
Port 9200 (Elasticsearch)

Insight: Some services are accessible internally but not exposed externally.

2️⃣ Service Detection Scan - nmap -sV 10.0.2.15

Findings:

Port 443 open (HTTPS)
Web application with login endpoint (/app/login)

Insight: External attack surface is limited to HTTPS service.

3️⃣ Vulnerability Scan - nmap --script vuln 10.0.2.15

🚨 Vulnerabilities Identified

Potential Slowloris DoS

CVE: CVE-2007-6750
Type: Denial of Service
State: Likely Vulnerable

Note: This is a generic detection and may result in false positives. Further validation is required.

HTTP Method Tampering (High Risk)
State: Vulnerable (Exploitable)
Impact: Authentication bypass

Description: Improper handling of HTTP methods may allow bypassing authentication controls.

🔗 Correlation with SOC Detection

Component	Role
Cowrie	Attack simulation (SSH brute-force)
Wazuh	Alert generation
Nmap	Exposure and vulnerability validation

🛠️ Security Recommendations

Restrict external access to HTTPS service (IP allowlisting)
Implement rate limiting to mitigate DoS attacks
Fix improper HTTP method handling
Enable monitoring for web-based attacks in Wazuh



🚀 Project Setup



1️⃣ Install Wazuh



\* Install Wazuh Manager and Dashboard

\* Verify logs are visible in the dashboard



\---



2️⃣ Install Cowrie Honeypot



```bash

git clone https://github.com/cowrie/cowrie

cd cowrie

pip install -r requirements.txt

./bin/cowrie start

```



\---



3️⃣ Integrate Cowrie Logs with Wazuh



Edit Wazuh configuration file:



📄 `/var/ossec/etc/ossec.conf`

 <!-- ✅ Your Cowrie log -->
  <localfile>
    <log_format>json</log_format>
    <location>/home/kali/Desktop/cowrie_soc/cowrie/var/log/cowrie/cowrie.json</location>
  </localfile>

</ossec_config>


Restart Wazuh:



```bash

sudo systemctl restart wazuh-manager

```



\---



🔐 Detection Rules



📄 File: `/var/ossec/etc/rules/local\_rules.xml`



```xml

<!-- Rule 1: Detect single failed login -->
<group name="cowrie,ssh,attack">
<!--  Base rule  -->
<rule id="100010" level="5">
<field name="eventid">cowrie.login.failed</field>
<description>Cowrie SSH login failed</description>
<group>authentication_failed,</group>
</rule>

<!-- Rule 2: Detect brute force attack -->
<!--  Correlation rule WITHOUT same_source_ip  -->
<rule id="100020" level="12" frequency="5" timeframe="60">
<if_matched_sid>100010</if_matched_sid>
<description>🚨 Cowrie SSH Brute Force Attack</description>
<group>bruteforce,attack,</group>
</rule>
</group>
```



Restart Wazuh after adding rules:



```bash

sudo systemctl restart wazuh-manager

```




🔍 Detection Logic Explained



✅ Rule 100010



\* Detects \*\*single failed SSH login\*\*

\* Trigger condition:



```

eventid = cowrie.login.failed

```



\---



🚨 Rule 100020 (Correlation Rule)



\* Detects \*\*brute-force attack\*\*

\* Logic:



 \* 5 failed logins

 \* Within 60 seconds

 \* From same IP



👉 This is called \*\*event correlation in SIEM\*\*



\---



📊 Sample Logs



```json

{

 "eventid": "cowrie.login.failed",

 "username": "root",

 "password": "test",

 "src\_ip": "127.0.0.1"

}

```

📸 Screenshots



Add screenshots in `/screenshots` folder:



\* Wazuh alerts dashboard

\* Rule 100010 triggering

\* Rule 100020 (brute force detection)

\* Cowrie logs in terminal



\---



✅ Output



\* Logs successfully ingested into Wazuh

\* Custom rules triggered alerts

\* Brute-force attack detected using correlation logic

\* Events visualized in Wazuh Dashboard



\---



🧠 Key Learnings



\* How SIEM works internally

\* Log → Decode → Rule → Alert workflow

\* Writing custom detection rules

\* Correlation-based detection

\* Real-world SOC monitoring concepts



\---


📂 Repository Structure


wazuh-cowrie-soc-lab/

│

├── README.md

│

├── rules/

│   └── local\_rules.xml

│

├── config/

│   └── ossec.conf

│

├── logs/

│   └── sample\_wazuh\_logs.json n hydra info

│

├── screenshots/

│   ├── alerts.png

│   ├── brute\_force.png

│   └── cowrie\_logs.png

│

└── architecture.png



🙌 Author



\*\*Dheeraj Singh\*\*

Cybersecurity Enthusiast | SOC | SIEM

