# 🛡️ Digital Forensics Report: The Credential Hunter
**Detection of Compromised Insider Accounts via Lateral Movement Analysis**

---

## 1. Executive Summary
The primary objective of this project is to identify specific user accounts within a network that have been compromised and are being utilized to stage internal attacks. By processing approximately **10 million authentication logs**, the analysis detects three critical threat indicators:
1.  **Lateral Movement:** Identification of users accessing an anomalous number of unique computers.
2.  **Brute Force Patterns:** Detection of repeated authentication failures followed by a successful login.
3.  **Protocol Anomalies:** Monitoring for unusual usage of legacy authentication protocols, such as NTLM.

---

## 2. Methodology & Data Processing
To isolate legitimate threats from high-volume network noise, the forensic pipeline utilizes the following stages:
* **Noise Reduction:** Machine accounts (identified by the `$` suffix) are filtered out to focus exclusively on human user behavior.
* **Feature Engineering:**
    * **Lateral Movement:** Monitored via a 1-hour rolling window to track unique destination computers accessed by a single user.
    * **Brute Force:** Tracked through a rolling sum of `Fail` events immediately followed by a `Success`.
* **Statistical Profiling:** Implementation of **Z-Scores** to quantify how significantly a user's behavior deviates from the established organizational baseline.

---

## 3. Analysis Insights
### Baseline Activity
The analysis established that a typical user in this environment accesses an average of **6.10 unique computers**. This baseline allows for the identification of extreme outliers.

### Lateral Movement Detection
The system scans for "hopping" behavior, where a user accesses more than five unique hosts within a single hour. 
* **Case Study (Suspect #1):** User `U1653@DOM1` was identified as an extreme anomaly, accessing **458 unique hosts** with a **Z-Score of 62.16**.

### Brute Force Detection
The "Credential Hunter" signature is defined as **10 consecutive failures followed by 1 success** for the same user. This statistical improbability often indicates automated dictionary attacks or password spraying.

---

## 4. Remediation & Prevention
### Phase 1: Immediate Containment
* **Isolate Source Workstations:** Immediately quarantine "Patient Zero" workstations to preserve memory artifacts (RAM) for malware analysis.
* **Account Lockout:** Force-disable and reset credentials for identified high-risk accounts.

### Phase 2: Hardening & SIEM Rules
The logic from this analysis can be implemented as permanent rules in a Security Operations Center (SOC):
* **Rule A:** `ALERT IF Unique_Dst_Hosts > 5 in 1 Hour`.
* **Rule B:** `ALERT IF Fail_Streak >= 10 AND Next_Event == Success`.
* **Protocol Hygiene:** Disable NTLMv1/v2 where possible and enforce **Kerberos Only** to prevent Pass-the-Hash attacks.

---

## 🛠️ Dependencies
* `pandas`, `numpy` (Data Processing)
* `matplotlib`, `seaborn` (Visualization)
* `networkx` (Advanced Network Graphing)
