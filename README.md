🛡️ Hybrid Windows & Exchange SIEM Pipeline (ELK Stack)
English | Türkçe

English
📝 Project Overview
This project provides a comprehensive SIEM (Security Information and Event Management) solution built on the ELK Stack to monitor and analyze logs from Windows Domain Controllers and Microsoft Exchange Servers in real-time.

🚀 Key Features
Risk Scoring Engine: Dynamically calculates a risk score (0-100) for security events using custom Ruby scripts.

Active Directory Security: Detects LAPS password reads, critical group changes (e.g., Domain Admins), and UAC modifications.

Exchange Traffic Analysis: Parses Message Tracking logs to monitor email flows and identify suspicious sender patterns.

Attack Detection: Pre-configured filters for Kerberoasting, Brute Force, and Audit Log clearing.

📂 Configuration Structure
10-input.conf: Listens for Beats input on port 5044.

21-dc-security.conf: The core engine for security event enrichment and risk scoring.

31-exc-messagetracking.conf: Handles CSV parsing and recipient splitting for Exchange logs.

compose.yml: Orchestrates Elasticsearch, Logstash, and Kibana via Docker.

Türkçe
📝 Proje Hakkında
Bu çalışma; Windows Domain Controller ve Microsoft Exchange Server loglarını merkezi bir noktada toplamak, anlamlandırmak ve siber güvenlik odaklı analiz etmek için geliştirilmiş bir ELK Stack mimarisidir.

🚀 Öne Çıkan Özellikler
Risk Skorlama Motoru: Güvenlik olaylarını Ruby scriptleri kullanarak 0 ile 100 arasında otomatik puanlar.

Active Directory Güvenliği: LAPS şifre okuma, kritik grup değişiklikleri ve UAC bayraklarındaki değişimleri anında tespit eder.

Exchange Trafik Analizi: Message Tracking loglarını ayrıştırarak e-posta trafiğini şeffaf hale getirir.

Atak Tespiti: Kerberoasting, Brute Force ve Log temizleme gibi aktiviteler için hazır filtreler sunar.

⚙️ Kurulum / Installation
Bash

# 1. Clone the repo
git clone https://github.com/bugrazen/ELKStack.git

# 2. Start the stack
docker-compose up -d
📊 Log Processing Logic (Mantıksal Akış)
The system categorizes logs into three main risk levels:

🔴 Critical (70-100): Immediate action required (e.g., Log Cleared, Admin Added).

🟡 Medium (40-69): Suspicious activities (e.g., Failed Logons, Policy Changes).

🟢 Low (0-39): Routine system and application events.