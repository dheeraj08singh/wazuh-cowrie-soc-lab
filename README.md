\# 🚨 SOC Detection Lab using Wazuh + Cowrie Honeypot



\## 📌 Overview



This project demonstrates how to build a basic \*\*Security Operations Center (SOC) lab\*\* using:



\* \*\*Cowrie Honeypot\*\* → to simulate real-world SSH attacks

\* \*\*Wazuh SIEM\*\* → to collect, analyze, and detect attacks



The goal of this project is to understand how security monitoring works in real environments by building a complete detection pipeline from scratch.



\---



\## 🧠 Architecture



```

Attacker → Cowrie Honeypot → Log File → Wazuh Agent → Wazuh Manager → Alerts → Dashboard

```



\---



\## ⚙️ Tools \& Technologies



\* Wazuh (SIEM)

\* Cowrie (SSH Honeypot)

\* Kali Linux

\* JSON Log Analysis



\---



\## 🚀 Project Setup



\### 1️⃣ Install Wazuh



\* Install Wazuh Manager and Dashboard

\* Verify logs are visible in the dashboard



\---



\### 2️⃣ Install Cowrie Honeypot



```bash

git clone https://github.com/cowrie/cowrie

cd cowrie

pip install -r requirements.txt

./bin/cowrie start

```



\---



\### 3️⃣ Integrate Cowrie Logs with Wazuh



Edit Wazuh configuration file:



📄 `/var/ossec/etc/ossec.conf`



```xml

<localfile>

&#x20; <log\_format>json</log\_format>

&#x20; <location>/home/kali/Desktop/cowrie\_soc/cowrie/var/log/cowrie/cowrie.json</location>

</localfile>

```



Restart Wazuh:



```bash

sudo systemctl restart wazuh-manager

```



\---



\## 🔐 Detection Rules



📄 File: `/var/ossec/etc/rules/local\_rules.xml`



```xml

<group name="cowrie,ssh,attack">



&#x20; <!-- Rule 1: Detect single failed login -->

&#x20; <rule id="100010" level="5">

&#x20;   <field name="eventid">cowrie.login.failed</field>

&#x20;   <description>Cowrie SSH login failed</description>

&#x20;   <group>authentication\_failed,</group>

&#x20; </rule>



&#x20; <!-- Rule 2: Detect brute force attack -->

&#x20; <rule id="100020" level="12" frequency="5" timeframe="60">

&#x20;   <if\_matched\_sid>100010</if\_matched\_sid>

&#x20;   <same\_source\_ip />

&#x20;   <description>🚨 Brute Force Attack Detected</description>

&#x20;   <group>bruteforce,attack,</group>

&#x20; </rule>



</group>

```



Restart Wazuh after adding rules:



```bash

sudo systemctl restart wazuh-manager

```



\---



\## 🔍 Detection Logic Explained



\### ✅ Rule 100010



\* Detects \*\*single failed SSH login\*\*

\* Trigger condition:



```

eventid = cowrie.login.failed

```



\---



\### 🚨 Rule 100020 (Correlation Rule)



\* Detects \*\*brute-force attack\*\*

\* Logic:



&#x20; \* 5 failed logins

&#x20; \* Within 60 seconds

&#x20; \* From same IP



👉 This is called \*\*event correlation in SIEM\*\*



\---



\## 📊 Sample Logs



```json

{

&#x20; "eventid": "cowrie.login.failed",

&#x20; "username": "root",

&#x20; "password": "test",

&#x20; "src\_ip": "127.0.0.1"

}

```



\---



\## 📸 Screenshots



Add screenshots in `/screenshots` folder:



\* Wazuh alerts dashboard

\* Rule 100010 triggering

\* Rule 100020 (brute force detection)

\* Cowrie logs in terminal



\---



\## ✅ Output



\* Logs successfully ingested into Wazuh

\* Custom rules triggered alerts

\* Brute-force attack detected using correlation logic

\* Events visualized in Wazuh Dashboard



\---



\## 🧠 Key Learnings



\* How SIEM works internally

\* Log → Decode → Rule → Alert workflow

\* Writing custom detection rules

\* Correlation-based detection

\* Real-world SOC monitoring concepts



\---



\## 📂 Repository Structure



```

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



\## 🙌 Author



\*\*Dheeraj Singh\*\*

Cybersecurity Enthusiast | SOC | SIEM



\---



\## ⭐ If you found this useful



Feel free to ⭐ the repository and connect with me on LinkedIn!

