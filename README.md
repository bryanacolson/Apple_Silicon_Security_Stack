# Apple_Silicon_Security_Stack
A simple security stack run through Docker that was made specifically for Apple Silicon computers.
m1-detection-stack
Containerized network detection engineering lab using:
Elasticsearch 8.12
Kibana 8.12
Zeek (PCAP replay)
Suricata (PCAP replay)
Designed for Apple Silicon (M1/M2 ARM64) environments.

1. Prerequisites
System Requirements
macOS (Apple Silicon recommended)
Docker Desktop installed
Docker Compose v2 (comes with Docker Desktop)
Verify:
docker --version
docker compose version

2. Repository Structure
Your repository should look like:
m1-detection-stack/
│
├── docker-compose.yaml
├── pcap/
│   └── sample.pcap
│
└── suricata/
Required Files
docker-compose.yaml (already present)
pcap/sample.pcap (must exist or containers will fail)
suricata/ directory (used for Suricata log output)

3. Running the Stack
From project root:
docker compose up
This will start:
Elasticsearch → http://localhost:9200
Kibana → http://localhost:5601
Zeek → processes pcap/sample.pcap
Suricata → processes pcap/sample.pcap
To run in background:
docker compose up -d
To stop:
docker compose down
To wipe Elasticsearch data:
docker compose down -v

4. Accessing Services
Elasticsearch
Test connectivity:
curl http://localhost:9200
Expected: JSON cluster information.
Kibana
Open browser:
http://localhost:5601
Note: No data ingestion pipeline is configured yet.
Kibana will start, but no indices will exist until log shipping is implemented.

5. How PCAP Replay Works
Both Zeek and Suricata are configured for offline analysis:
-r /pcap/sample.pcap
To analyze a different PCAP:
Place new file in:
pcap/
Edit docker-compose.yaml and change:
/pcap/sample.pcap
to:
/pcap/yourfile.pcap
Restart:
docker compose down
docker compose up

6. Architecture Overview
PCAP
  │
  ├── Zeek → Network telemetry logs (inside container)
  └── Suricata → IDS alerts (written to ./suricata)

Elasticsearch + Kibana running but not yet ingesting logs
Current version is a PCAP replay lab, not a full SOC ingestion pipeline.

How to Use
Make sure your folder structure is:
m1-detection-stack/
├── docker-compose.yaml
├── pcap/
│   └── sample.pcap

Run the stack:
docker compose up

Suricata & Zeek will process the PCAP once. Logs are written to volumes:
Suricata → suricata_logs
Zeek → zeek_logs
To extract logs to host:

docker cp $(docker ps -alq):/var/log/suricata ./suricata
docker cp $(docker ps -alq):/logs ./zeek

Elasticsearch → http://localhost:9200
Kibana → http://localhost:5601
