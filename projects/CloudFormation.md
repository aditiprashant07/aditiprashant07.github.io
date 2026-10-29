
---
layout: page
title: Enterprise Serverless IoT Healthcare Telemetry Platform (IaC)
permalink: /projects/MedBot/
---

# Enterprise Serverless IoT Healthcare Telemetry Platform (IaC)

## 1. Overview of the Project
This project is an automated, event-driven Infrastructure-as-Code (IaC) architecture designed for real-time patient physiological monitoring and alerting. Built with modular AWS CloudFormation templates, it manages the complete telemetry lifecycle—from edge device data ingestion and dual-path data routing to automated anomaly alerting and secure clinical dashboard queries[cite: 2].

[ Medical IoT Edge Devices ]
                              │
                              ▼
                     [ AWS IoT Core Rules ]
                    /          │           \
    (Hot Path)     /           │            \ (Cold Path)
                  ▼            ▼             ▼
    [ Amazon DynamoDB ]  [ EventBridge ]  [ Kinesis Data Firehose ]
      (Live Lookups)           │                     │
                               ▼                     ▼
                       [ Lambda Notifier ]   [ Amazon S3 Sink ]
                               │              (Raw Data Lake)
                               ▼
                       [ Amazon SNS Alert ]
                       (Critical SMS/Email)

       [ Web / Clinician Dashboard (Amplify) ]
                               │
                               ▼
                    [ Amazon API Gateway ]
                       (CORS / Throttled)
                               │
                               ▼
                    [ Query Lambda Engine ]
                               │
                               ▼
                    [ Amazon DynamoDB ]



The system separates high-frequency live status reads from long-term compliance storage:
* **Ingestion Layer:** AWS IoT Core receives incoming sensor payloads via MQTT/HTTPS.
* **Hot Storage Tier:** Real-time vitals are written to Amazon DynamoDB for low-latency queries by medical dashboards.
* **Cold Storage Tier:** Continuous streams are ingested through Amazon Kinesis Data Firehose and written into Amazon S3 for compliance, data archiving, and machine learning model training.
* **Event & Alert Engine:** Amazon EventBridge evaluates incoming metrics against defined physiological thresholds and routes anomalies to AWS Lambda to trigger emergency notifications via Amazon SNS.
* **API & Frontend Delivery:** An Amazon API Gateway REST API with full CORS validation fronts a Lambda query service, while AWS Amplify hosts the user interface.

---

## 2. Technologies Used
* **Infrastructure as Code (IaC):** AWS CloudFormation (modular templates coordinating stacks: `master-template.yaml`, `iot-infrastructure-updated.yaml`, `data-storage.yaml`, `firehose-s3.yaml`, `eventbridge-rules-updated.yaml`, `api-gateway-cors.yaml`, and `amplify-frontend.yaml`).
* **Ingestion & Streaming:** AWS IoT Core (Topic Rules & Device integration), Amazon Kinesis Data Firehose (batch buffering and S3 delivery).
* **Compute:** AWS Lambda (Python 3.11 runtimes for event routing, query handling, and notification processing).
* **Databases & Storage:** Amazon DynamoDB (single-table schema with partition and sort keys), Amazon S3 (partitioned object storage).
* **Event Orchestration & Messaging:** Amazon EventBridge (rule filters and event bus), Amazon SNS (multi-protocol dispatch).
* **API Management & Hosting:** Amazon API Gateway (REST APIs, Lambda proxy integrations, mock CORS configurations), AWS Amplify.
* **Security & Observability:** AWS IAM (least-privilege roles), AWS CloudWatch Logs[cite: 2].

---

## 3. The Problem It Solved
* **Ingestion Bottlenecks & Outages:** Traditional monolithic polling servers often suffer dropped packets during traffic spikes, risking missing critical patient telemetry.
* **Prohibitive Storage Costs:** Storing all granular, high-frequency historical telemetry in active transactional databases drives up read/write capacity and table maintenance costs.
* **Lagging Alert Response:** Periodic database polling creates unacceptable delays when identifying physiological emergencies like cardiac spikes or sudden drops in blood oxygen saturation ($SpO_2$).

**Delivered Solution:**
* Replaced servers with a zero-maintenance, fully serverless architecture that scales from zero to high telemetry volumes without manual capacity intervention[cite: 2].
* Partitioned data streams into hot path (DynamoDB for current vitals) and cold path (Firehose/S3 for bulk historical analytical storage)[cite: 2].
* Reached sub-second critical notification dispatch via EventBridge and Lambda anomaly detection directly off the ingestion rule[cite: 2].
* Guaranteed standard, zero-drift deployments through declarative AWS CloudFormation stacks[cite: 2].

---

## 4. Engineering Approach & Technical Implementation

### A. Declarative Infrastructure Modularization
Rather than managing a single brittle template, the deployment separates responsibilities into modular templates coordinated by `master-template.yaml`[cite: 2]:
* **`data-storage.yaml`:** Declares the DynamoDB table schema (`device_id` as Partition Key, `timestamp` as Sort Key) and the S3 telemetry sink[cite: 2].
* **`iot-infrastructure-updated.yaml`:** Configures IoT topic subscriptions and rules that parse incoming telemetry payloads[cite: 2].
* **`firehose-s3.yaml`:** Sets up buffered batch writes to S3 to reduce S3 PUT API requests[cite: 2].
* **`eventbridge-rules-updated.yaml`:** Encapsulates the metric evaluation logic to route alerts without altering core storage pathways[cite: 2].
* **`api-gateway-cors.yaml` & `amplify-frontend.yaml`:** Deploys the integration layer, handling OPTIONS preflight CORS headers and frontend asset delivery[cite: 2].

### B. Hot/Cold Path Dual-Stream Mechanics
When an IoT edge sensor sends a payload:
```json
{
  "device_id": "PATIENT-UNIT-402",
  "timestamp": 1713691200,
  "heart_rate": 134,
  "spo2": 90,
  "body_temp": 38.8
}

* **Hot Path Execution:** IoT Core topic rules direct the payload to DynamoDB, updating current status for instant clinician visibility[cite: 2].
* **Cold Path Execution:** The same payload is delivered to Kinesis Data Firehose, which buffers records based on size or intervals before writing partitioned files to Amazon S3[cite: 2].

### C. Threshold-Driven Anomaly Alerting
An EventBridge rule inspects incoming events[cite: 2]. If vitals exceed safe ranges (e.g., $HeartRate > 120$ bpm, $SpO_2 < 92\%$, or $BodyTemp > 38.5^\circ\text{C}$), the event immediately invokes `sns-notifier.py`[cite: 2]. The function formats a high-priority dispatch and publishes to an Amazon SNS topic, sending instant SMS and email notifications to assigned staff[cite: 2].

### D. Secure Data Consumption & Query Layer
API Gateway routes REST requests to `query-lambda.py`[cite: 2]. The Lambda parses parameters (such as `device_id` and time windows), executes targeted DynamoDB queries over the indexed sort keys, and returns responses containing necessary CORS headers (`Access-Control-Allow-Origin: *`) to enable smooth communication with the Amplify frontend[cite: 2].

---

## 5. Important Interview Topics & System Design Trade-Offs
* **DynamoDB Key Design:** Using `device_id` as the Partition Key and `timestamp` as the Sort Key avoids hot partitions while enabling efficient point queries and time-range scans without full table scans[cite: 2].
* **Cost Optimization via Firehose Buffering:** Writing directly to S3 per incoming packet causes an explosion of S3 PUT API calls[cite: 2]. Firehose aggregates multiple records into fewer batch files, cutting operational costs and improving query performance for downstream analytics[cite: 2].
* **Cold Start & Execution Optimization:** Python functions utilize only standard libraries and the AWS SDK (`boto3`), keeping bundle sizes small, reducing initialization time, and keeping cold starts low[cite: 2].
* **IoT SQL Ingestion Filtering:** Running SQL filtering logic directly inside AWS IoT Core rules avoids unnecessary compute execution costs for invalid or redundant data packets[cite: 2].
* **Failure Modes & Reliability:** CloudFormation manages separate IAM roles for querying and alerting, enforcing least-privilege boundaries[cite: 2]. Ingestion pipelines operate independently; transient storage issues do not block downstream alert evaluations[cite: 2].

---

## 6. Relevant Extensions & Future Roadmap
* **Automated Data Lifecycle Policies:** Adding DynamoDB Time-To-Live (TTL) to automatically expire raw records older than 30–90 days, keeping hot database storage light and cost-effective.
* **Serverless Analytics with AWS Athena:** Creating an AWS Glue Data Catalog table on the S3 bucket to allow physicians and researchers to run ad hoc SQL queries across longitudinal datasets without managing databases.
* **Patient Identity & Access Control:** Integrating Amazon Cognito with the API Gateway authorizer to enforce role-based access control (RBAC) across patient and clinician portals.
