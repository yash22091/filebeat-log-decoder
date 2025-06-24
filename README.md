# Filebeat Log Decoder for Wazuh

This repository provides **custom Wazuh decoders and rules** specifically crafted for parsing **Filebeat-generated logs**. These configurations are designed to help **monitor and detect logs that are received by the Wazuh Manager but not ingested into the indexer**, due to various issues such as:

* Field mapping conflicts
* Data type mismatches
* Field count limitations
* Incompatible or malformed structures

By deploying these decoders and rules, users gain the ability to **track logs that are effectively lost between ingestion and visualization**, helping improve observability, data quality, and detection capabilities.

---

## Key Features

* **Custom Decoders & Rules**: Tailored to handle common Filebeat log patterns and capture ingestion anomalies.
* **NDJSON Dashboard**: A pre-built dashboard is provided in NDJSON format. Users only need to import it into Kibana/Opensearch Dashboards.
* **Enhanced Visibility**: Allows SOC teams to identify which logs were ingested into the indexer but failed to appear in dashboards due to field/structure issues.
* **Ingestion Monitoring**: Helps in quantifying the number of unvisualized or unparsed logs, aiding forensic or monitoring tasks.

---

## Repository Structure

```
filebeat-log-decoder/
├── decoders/
│   └── custom_filebeat_decoder.xml
├── rules/
│   └── custom_filebeat_rules.xml
├── dashboards/
│   └── filebeat_monitoring_dashboard.ndjson
└── README.md
```

---

## Installation Steps

1. **Copy Decoders and Rules**

   Place the decoder and rule files in the appropriate directories on your Wazuh/OSSEC Manager:

   ```bash
   cp decoders/custom_filebeat_decoder.xml /var/ossec/etc/decoders/
   cp rules/custom_filebeat_rules.xml /var/ossec/etc/rules/
   ```

2. **Enable Filebeat Log Collection**

   Ensure that your manager’s `ossec.conf` is configured to allow logs from Filebeat:

   ```xml
   <localfile>
     <log_format>syslog</log_format>
     <location>/var/log/filebeat/filebeat*</location>
   </localfile>
   ```

3. **Restart the Manager**

   ```bash
   systemctl restart wazuh-manager
   ```

4. **Import Dashboard**

   In Kibana or Opensearch Dashboards:

   * Navigate to **Stack Management > Saved Objects**
   * Click **Import** and upload `filebeat_monitoring_dashboard.ndjson`
   * Dashboard will now be available under your dashboards section

---

## Outcome

After integrating the decoders and rules:

* You’ll start seeing alerts/log entries for logs that were parsed by the manager but failed at the indexing stage.
* The dashboard will visualize counts and types of such log events.
* Helps proactively detect parsing or mapping issues before they impact visibility.

---

## Notes

* Ensure your index mapping and field limits are aligned with the nature of logs being ingested.
* This tool complements but **does not replace proper index template and pipeline tuning** for Filebeat.
