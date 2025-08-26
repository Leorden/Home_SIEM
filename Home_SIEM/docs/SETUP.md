# Setup

1. Copy `.env.example` to `.env`, choose strong passwords.
2. `cd compose`, run `docker compose up -d`.
3. Open OpenSearch Dashboards at http://localhost:5601.
4. Install Winlogbeat on Windows or Filebeat on Linux, use the example configs under `beats/`.
5. Create the GeoIP ingest pipeline using the JSON under `pipelines/` if you want geo enrichment.
6. Import dashboards when available, or start with Discover and build visualizations.
7. Add alert rules under `detections/`, keep them in source control.

Tips, keep services on your LAN only, use strong passwords, update regularly.
