# Docker Compose and Conf files for ELKStack

**English** | [Türkçe](#türkçe)

---

## The Short Version

I put this together to stop digging through raw event logs. It centralizes Windows Security and Exchange Message Tracking logs into a single ELK stack. Instead of just dumping a massive amount of data into Elasticsearch and hoping for the best, it enriches the events on the fly. It adds a 0-100 risk score and tags the stuff you actually care about (like LAPS password reads or AD group changes) before you even open Kibana.

---

## Under the Hood (Logstash)

**`10-input.conf`** — Standard Beats listener on port 5044. Point your Winlogbeat (or whatever you use) here.

**`21-dc-security.conf`** — This is where the heavy lifting happens. It runs a custom Ruby script against incoming Windows Security events. If someone reads a LAPS password, touches the Domain Admins group, or modifies UAC flags, the pipeline tags and scores it immediately. You don't have to write massive, complex Kibana queries later; the data arrives already normalized and flagged.

**`31-exc-messagetracking.conf`** — Exchange message tracking logs are a CSV nightmare, especially those semicolon-delimited recipient fields. This config parses the raw CSV, splits recipients into individual searchable documents, and makes your mail flow data actually useful.

**`compose.yml`** — Standard Docker Compose setup. Spins up a single-node Elasticsearch, Logstash, and Kibana stack. Easy to scale if your environment needs it.

---

## What It Actually Catches

- LAPS password reads (Event 4662)
- Domain/Schema Admin group changes
- UAC modifications on privileged accounts
- Kerberoasting attempts
- Brute-force logins
- Cleared audit logs (Event 1102)
- Anomalous Exchange mail flows

---

## Risk Scoring Logic

| Level | Score | Examples |
|---|---|---|
| 🔴 Critical | 70 – 100 | Audit log cleared, admin group member added |
| 🟡 Medium | 40 – 69 | Failed logons, policy changes |
| 🟢 Low | 0 – 39 | Routine system and application events |

---

## Installation

```bash
# 1. Clone the repo
git clone https://github.com/bugrazen/ELKStack.git

# 2. Start the stack
docker-compose up -d
```

**Requirements:** Just Docker and Docker Compose. Send your logs to port 5044 and you're good to go.

---

## Türkçe

[English](#the-short-version) | **Türkçe**

---

## Özet

Ham log yığınları içinde boğulmamak için hazırladığım bir ELK altyapısı. Windows Domain Controller ve Exchange loglarını tek bir yerde toplayıp Elasticsearch'e basmadan önce anlamlı hale getiriyor. Yani sadece "her şeyi logla, bir ara bakarız" demiyoruz; araya giren bir Ruby scripti ile olaylara 0-100 arası risk skoru verip, LAPS şifre okumaları veya AD grup değişiklikleri gibi kritik olayları anında etiketliyoruz.

---

## Nasıl Çalışıyor? (Logstash)

**`10-input.conf`** — Beats dinleyicisi. Winlogbeat'i 5044 portuna yönlendirmeniz yeterli.

**`21-dc-security.conf`** — Asıl işin döndüğü yer. Gelen Windows Security loglarına bakıp olayın tipine ve hedefine göre risk skoru atıyor. Biri Domain Admins'e dokunduğunda veya UAC değiştirdiğinde, log daha Kibana'ya düşmeden işaretlenmiş oluyor. Sonradan "şunu nasıl yakalarım" diye karmaşık sorgular yazmakla uğraşmıyorsunuz.

**`31-exc-messagetracking.conf`** — Exchange'in noktalı virgülle ayrılmış o meşhur karmaşık CSV loglarını adam eden kısım. Alıcıları tek tek bölüp ayrı dokümanlara dönüştürüyor, böylece kim kime ne atmış rahatça filtreleyebiliyorsunuz.

**`compose.yml`** — Klasik Docker Compose. Elasticsearch, Logstash ve Kibana'yı tek node olarak ayağa kaldırıyor. Lab veya küçük ortamlar için tak-çalıştır.

---

## Neleri Yakalıyor?

- LAPS şifre okumaları (Event 4662)
- Kritik AD (Domain/Schema Admins) grup üyelik değişiklikleri
- Yetkili hesaplarda UAC değişimleri
- Kerberoasting denemeleri
- Brute-force sekansları
- Audit log temizleme (Event 1102)
- Şüpheli Exchange mail trafikleri

---

## Risk Skorlama Mantığı

| Seviye | Skor | Örnekler |
|---|---|---|
| 🔴 Kritik | 70 – 100 | Audit log temizlendi, admin grubuna üye eklendi |
| 🟡 Orta | 40 – 69 | Başarısız oturum açma denemeleri, politika değişiklikleri |
| 🟢 Düşük | 0 – 39 | Rutin sistem ve uygulama eventleri |

---

## Kurulum

```bash
# 1. Repoyu klonla
git clone https://github.com/bugrazen/ELKStack.git

# 2. Stack'i başlat
docker-compose up -d
```

**Gereksinimler:** Sadece Docker ve Docker Compose. Winlogbeat ile logları 5044'e gönderin, gerisini pipeline hallediyor.
