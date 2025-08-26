# Home_SIEM

A small, home friendly SIEM that collects, enriches, searches, and alerts on logs from your home lab. The goal is simple, fast visibility of security events, low overhead, and a clean path to dashboards and alerting.

---

##  Features
- Collect Windows, Linux, and network logs with lightweight agents
- Enrich with GeoIP and ASN, create an attack map style visualization
- Search and build dashboards in OpenSearch Dashboards
- Alert on common events, failed logons, privilege changes, suspicious process trees
- Docker based setup, one command up and running
- Local first, no cloud dependency

##  Architecture
- **Agents**, choose what you need
  - Windows, Sysmon plus Winlogbeat
  - Linux, Filebeat or Fluent Bit, optional auditd rules
  - Network, Suricata or Zeek for IDS style logs
- **Ingest and enrichment**, OpenSearch ingest pipelines or Logstash with GeoIP and field normalization
- **Storage and search**, OpenSearch single node for local use
- **Dashboards and alerts**, OpenSearch Dashboards with saved searches and alert rules

```
[Windows Sysmon]   → Winlogbeat →
[Linux auditd]     → Filebeat   →  Ingest pipeline (GeoIP, rename) →  OpenSearch  ← OpenSearch Dashboards
[Suricata/Zeek]    → Filebeat   →                                                    (Dashboards, Alerts)
```

##  Tech stack
- OpenSearch and OpenSearch Dashboards
- Beats, Winlogbeat, Filebeat
- Optional, Logstash for advanced pipelines
- Optional, Suricata or Zeek for network telemetry
- Docker and Docker Compose

---

##  Quick start
### 1) Prerequisites
- Docker and Docker Compose installed
- At least 4 GB RAM available

### 2) Clone and configure
```bash
git clone https://github.com/Leorden/Home_SIEM.git
cd Home_SIEM
cp .env.example .env   # edit passwords and ports
```
Set strong passwords in `.env`. Keep services bound to your LAN only.

### 3) Bring up the stack
```bash
docker compose up -d
```

Open **OpenSearch Dashboards** at http://localhost:5601

### 4) Add an agent

#### Windows, Sysmon plus Winlogbeat
1. Install Sysmon with a community config, for example SwiftOnSecurity
2. Install Winlogbeat
3. Edit `winlogbeat.yml` and set `output.opensearch` to your local endpoint
4. Enable Windows event channels and Sysmon logs, restart the service

Minimal `winlogbeat.yml` output block
```yaml
output.opensearch:
  hosts: ["http://localhost:9200"]
  username: "admin"
  password: "${OPENSEARCH_PASSWORD}"
```

#### Linux, Filebeat
1. Install Filebeat with your package manager
2. Enable system module, `filebeat modules enable system`
3. Set `output.opensearch`, restart the service

Minimal `filebeat.yml` output block
```yaml
output.opensearch:
  hosts: ["http://localhost:9200"]
  username: "admin"
  password: "${OPENSEARCH_PASSWORD}"
```

#### Network telemetry, optional
1. Install Suricata or Zeek
2. Point Filebeat or another shipper at the JSON output, for example `eve.json`

---

##  Dashboards
- **Overview**, system and security events with time presets
- **Authentication**, failed logons, success rates, source IPs
- **Processes**, parent child trees, unsigned binaries, LOLBins
- **Network**, Suricata alerts by rule, destination country, top talkers
- **Attack map**, world map using GeoIP enriched source IPs

### Enrichment
- GeoIP city, country, location
- ASN owner for quick triage

---

##  Alerts
Start simple, then expand.
- Multiple failed logons from one IP in a short window
- Local admin group membership changes
- Process execution from user profile temp locations
- Suricata high severity alerts

Alert examples will live under `./detections` as YAML or NDJSON.

---

##  Test data
You can generate safe test events locally.
- Windows, use `runas` with a wrong password several times
- Linux, attempt a few failed `ssh` logins from another host in your LAN
- Network, run `nmap -sS` from a spare VM against your lab gateway

---

##  Repository layout
```
Home_SIEM/
├─ compose/               # Docker Compose files
├─ dashboards/            # Saved objects for OpenSearch Dashboards
├─ detections/            # Alert rules and queries
├─ pipelines/             # Ingest pipelines or Logstash pipelines
├─ beats/                 # Example configs for Winlogbeat and Filebeat
├─ scripts/               # Helper scripts, GeoIP updater and tools
└─ docs/                  # Notes, screenshots, how tos
```

---

##  Security notes
This is a home project, use strong passwords, keep ports closed to the internet, and restrict access to your LAN. All data stays local by default.

---

##  Roadmap
- Add prebuilt dashboards for Sysmon and Suricata
- Add Sigma rule to OpenSearch converter script
- Add Zeek integration examples
- Add optional Wazuh branch for host based monitoring

---

##  Credits
Inspired by community home lab SIEM projects that visualize live data and apply modern SIEM concepts. Thank you to creators who share ideas openly.

## License
Choose a license that fits your goals, for example MIT for code and CC BY for docs.

