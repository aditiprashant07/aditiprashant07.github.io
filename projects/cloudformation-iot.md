---
layout: page
permalink: /projects/cloudformation-iot/
---

# Serverless IoT Anomaly Detection Pipeline with AWS CloudFormation

## 1. Project overview

This project provisions a cloud-based IoT anomaly-detection system using modular AWS CloudFormation templates. It brings together device registration, telemetry ingestion, serverless processing, anomaly detection, event routing, persistence, notifications, query APIs, and a web frontend deployment path.

The master template describes a stack composed of nested infrastructure templates and configurable parameters for environments, Kinesis capacity, retention, Lambda resources, notification email, and optional Amplify deployment. The anomaly detector evaluates incoming CPU telemetry using multiple statistical techniques and maintains per-device rolling state and baselines in Amazon S3.

**Repository:** [CloudFormation](https://github.com/aditiprashant07/CloudFormation)

## 2. Objectives

- Define repeatable infrastructure for an IoT anomaly-detection application as Infrastructure as Code.
- Ingest device telemetry and route it to downstream processing components.
- Detect unusual device behaviour using multiple statistical methods rather than a single fixed threshold.
- Persist device, telemetry, anomaly, and detector state in suitable AWS services.
- Route anomaly events and notify users.
- Expose device and anomaly information through serverless APIs and a frontend deployment.
- Make deployment configurable across environments and operational settings.

## 3. Were the objectives achieved?

The repository contains a substantial set of CloudFormation templates and Lambda implementation files that describe the infrastructure and processing workflow. The master template identifies the IoT anomaly-detection system, nested resources, data stores, event routing, Lambda code assets, and Amplify frontend configuration.

The anomaly-detection Lambda code implements rolling CPU history, persisted baselines, adaptive thresholds, and consensus across statistical detectors. The source includes z-score, EWMA deviation, rate-of-change, modified z-score/MAD, and Hampel-filter checks, with severity assignment when multiple methods trigger.

This supports the conclusion that the infrastructure and detection components have been implemented in code. The repository files alone do not establish that every stack was successfully deployed end-to-end in a live AWS account or provide measured detection precision/recall, latency, or uptime. Those outcomes should only be claimed with deployment logs and test evidence.

## 4. Concepts learned and applied

- Infrastructure as Code and declarative cloud provisioning.
- Modular and nested CloudFormation stacks, parameters, conditions, outputs, and resource dependencies.
- Event-driven and serverless architecture.
- IoT telemetry ingestion and stream processing.
- Kinesis Data Streams and Firehose-style delivery patterns.
- AWS Lambda execution, environment configuration, IAM permissions, and CloudWatch logging.
- DynamoDB table design, indexes, TTL, streams, and point-in-time recovery.
- Amazon S3 for durable telemetry/state storage.
- Amazon EventBridge rules and event buses for decoupled routing.
- SNS-based alerting and API Gateway integration.
- Time-series storage and querying patterns, including Amazon Timestream.
- Statistical anomaly detection: z-score, exponentially weighted moving average (EWMA), median absolute deviation (MAD), Hampel filtering, and rate-of-change checks.
- Adaptive thresholds, rolling windows, baseline estimation, and severity classification.
- Secure deployment configuration and parameterized environments.

## 5. Approach and what is different about it

### Approach

1. **Model the infrastructure as reusable templates.** The repository separates major infrastructure concerns into templates for storage, event routing, Firehose/S3, Lambda functions, API resources, and frontend deployment, with a master template coordinating the stack.
2. **Provision device and telemetry data stores.** The templates define DynamoDB resources for device registration, metadata, status, and anomaly-related records, with indexes and retention features where appropriate.
3. **Ingest and route telemetry.** Streaming and event components connect incoming device data to processing and storage destinations.
4. **Run serverless anomaly detection.** A Lambda function maintains a rolling window of up to 100 CPU observations per device and loads/saves state and baseline values in S3.
5. **Use multiple statistical signals.** The detector evaluates z-score, EWMA, rate of change, modified z-score/MAD, and Hampel-filter signals. It requires multiple triggered methods before emitting an anomaly and assigns a severity level.
6. **Publish and expose results.** EventBridge and notification components route anomaly events, while query and device APIs expose relevant records to clients.
7. **Deploy the frontend optionally.** The master template includes parameters and a condition for deploying the React frontend through Amplify.

### What makes this approach distinctive?

- **Infrastructure is expressed as code:** the application is designed to be provisioned and changed through version-controlled templates rather than manual console configuration.
- **Modular infrastructure:** distinct service concerns are separated into templates, while the master stack coordinates deployment.
- **Multi-signal anomaly detection:** the Lambda implementation combines several statistical detectors and uses agreement between methods to reduce dependence on a single threshold.
- **Per-device adaptive state:** rolling history and baseline thresholds are persisted by device, allowing thresholds to adapt to device-specific behaviour.
- **End-to-end cloud design:** ingestion, detection, event routing, persistence, APIs, notifications, and frontend deployment are considered as one system.

The design demonstrates an implementation approach; claims about reduced false positives or production reliability would require measured tests and deployment evidence.

## 6. Technologies and tools used

| Technology | Role |
|---|---|
| AWS CloudFormation | Infrastructure as Code and stack orchestration |
| AWS IoT / IoT telemetry components | Device-facing telemetry workflow |
| Amazon Kinesis Data Streams | Streaming telemetry ingestion |
| Amazon Kinesis Data Firehose | Delivery of stream data to storage |
| AWS Lambda | Serverless anomaly detection, data processing, and API handlers |
| Amazon S3 | Telemetry archives, baselines, and detector state |
| Amazon DynamoDB | Device registration, metadata, status, and anomaly records |
| Amazon EventBridge | Event bus and rule-based routing |
| Amazon SNS | Notification delivery |
| Amazon API Gateway | HTTP API endpoints |
| Amazon Timestream | Time-series persistence/querying in the architecture |
| AWS Amplify | Frontend hosting/deployment integration |
| Python and boto3 | Lambda implementation and AWS service integration |
| CloudWatch | Logging and operational observability |

## Limitations and next steps

- Add a deployment guide with exact prerequisites, nested-template upload steps, parameter examples, and teardown instructions.
- Validate the full stack in a clean AWS account and retain deployment evidence.
- Add automated tests for detector edge cases, baseline initialization, missing state, zero variance, and noisy telemetry.
- Evaluate the anomaly detector against labelled or simulated normal/anomalous sequences and report precision, recall, false-positive rate, and detection delay.
- Document IAM least-privilege policies, secret handling, encryption, and cost assumptions.
- Avoid presenting the architecture as production-validated until end-to-end deployment and operational testing are recorded.
