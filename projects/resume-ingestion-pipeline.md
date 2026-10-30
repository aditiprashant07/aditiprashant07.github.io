---
layout: page
permalink: /projects/resume-ingestion-pipeline/
---

# Resume Ingestion and Processing Pipeline — AWS-Based, Asynchronous Document Processing

## 1. Project overview

This project implements the first phase of a cloud-native resume ingestion system. It accepts individual resume uploads through an authenticated FastAPI endpoint, stores the original files in Amazon S3, and places extraction jobs onto Amazon SQS for asynchronous processing. Dedicated worker services extract text from different document formats, normalize the extracted content, calculate hashes, and persist candidate-ingestion metadata in an Aurora MySQL database.

The system also supports bulk ingestion from CSV and ZIP files. A bulk worker expands these inputs into individual resume jobs and sends them through the same extraction workflow. AWS CloudFormation templates define the surrounding infrastructure, including networking, IAM roles, S3 storage, Aurora, messaging, ECS/Fargate services, load balancing, and Textract integration.

**Repository:** [resume-ingestion-pipeline](https://github.com/aditiprashant07/resume-ingestion-pipeline)

## 2. Objectives

- Build a scalable ingestion entry point for individual resume uploads.
- Support bulk resume intake through CSV exports and ZIP archives.
- Process documents asynchronously so upload requests are not coupled to the full extraction workflow.
- Handle common resume formats such as PDF, DOCX, plain text, and image-based documents.
- Use OCR for scanned PDFs and image files where ordinary text extraction is insufficient.
- Normalize extracted resume text and retain original and processed objects in S3.
- Detect duplicate files or repeated resume text using content hashes.
- Persist candidate identifiers, storage references, hashes, and ingestion status in a relational database.
- Provision the cloud infrastructure through reusable, version-controlled CloudFormation templates.

## 3. Were the objectives achieved?

The repository contains an implemented Phase I architecture and corresponding application components for the main ingestion path:

- The FastAPI endpoint validates a bearer JWT, assigns a candidate ID, uploads the original file to S3, and queues an extraction message.
- The extractor worker detects file types and handles TXT, DOCX, and PDF parsing. PDFs with very little extracted text, as well as supported image formats, are routed to asynchronous Amazon Textract extraction.
- The bulk worker parses CSV resume exports and ZIP archives, stores each resume as an individual S3 object, and queues each one for extraction.
- The hash worker calculates SHA-256 hashes for the original file and normalized text, writes candidate metadata to Aurora MySQL, and publishes a completion notification through SNS.
- The CloudFormation root template coordinates nested stacks for networking, IAM, storage, Aurora, messaging, ECS, API resources, workers, and Textract.
- Deployment and API documentation describe the infrastructure and operational setup.

The repository therefore demonstrates substantial implementation of the Phase I ingestion and cloud-provisioning workflow. However, the repository's deployment guide still requires AWS resources, container images, nested templates, and runtime configuration to be supplied and deployed. The source code alone does not prove that the entire system has been deployed and load-tested in a live AWS environment. The documented pipeline should be described as implemented in code, with end-to-end production validation dependent on deployment and integration testing.

## 4. Concepts learned and applied

- Asynchronous, event-driven data processing.
- Designing ingestion APIs with FastAPI and JWT-based authentication.
- Multi-tenant request context and tenant-aware job metadata.
- Object storage patterns using Amazon S3.
- Queue-based decoupling with Amazon SQS.
- Worker services and message acknowledgement/retry behaviour.
- Bulk data ingestion from CSV and ZIP archives.
- File-type detection and document parsing.
- PDF and DOCX text extraction.
- OCR and asynchronous document processing with Amazon Textract.
- Text normalization and data-pipeline stage separation.
- SHA-256 content hashing for duplicate detection and data integrity.
- Relational schema design and indexing in Aurora MySQL.
- Event notifications using Amazon SNS.
- Containerized workloads with Docker and Amazon ECS on AWS Fargate.
- Infrastructure as Code using modular and nested AWS CloudFormation stacks.
- VPC networking, public/private subnets, NAT, security groups, and Application Load Balancers.
- IAM task roles, execution roles, and service-specific permissions.
- Operational logging with Amazon CloudWatch.
- Deployment configuration through AWS Systems Manager Parameter Store and Secrets Manager.

## 5. Approach and what is different about it

### Approach

1. **Accept and authenticate uploads.** A FastAPI endpoint exposes the resume-ingestion route. JWT validation establishes a tenant context, and the API generates a unique candidate ID for the upload.
2. **Store the original file and enqueue work.** The API uploads the original bytes to S3 and sends a message containing the candidate ID, tenant ID, and S3 object key to the extraction queue. The request can return a queued status without waiting for document processing to finish.
3. **Support bulk intake.** A separate bulk worker consumes CSV or ZIP jobs. CSV rows are interpreted as resume text, while ZIP entries are handled as individual files. Each valid resume is stored separately and fanned out into the same extraction queue.
4. **Detect file type and extract content.** The extractor worker identifies the file format. It uses Python document parsers for text, DOCX, and text-based PDFs. If a PDF contains too little extractable text, or the input is an image, it uses Amazon Textract's asynchronous extraction workflow.
5. **Pass extracted text to downstream processing.** Extracted content is written to the extracted-data S3 location and a message is sent to the normalization queue. This keeps extraction separate from later text-cleaning and normalization work.
6. **Hash and register candidate data.** The hash worker reads the normalized text and original file, computes SHA-256 hashes, and inserts candidate metadata and S3 references into Aurora MySQL.
7. **Publish completion events.** The hash worker publishes a resume-normalized event through SNS so downstream systems can react without being tightly coupled to the ingestion workers.
8. **Provision infrastructure as code.** A root CloudFormation template coordinates nested stacks for VPC networking, IAM, storage, database, messaging, ECS/Fargate, API routing, workers, and Textract permissions and notifications.

### What makes this approach distinctive?

- **Upload and processing are decoupled:** the API does not need to parse a resume synchronously. It stores the original document and hands processing to queue-driven workers.
- **One processing path for single and bulk uploads:** CSV and ZIP ingestion fan out into individual extraction messages, reusing the same downstream processing pipeline.
- **Format-aware extraction with OCR fallback:** native text extraction is used where suitable, while scanned PDFs and image-based documents can be processed through Textract.
- **Original and derived artifacts are separated:** original files, extracted text, and normalized text are stored as distinct pipeline artifacts, making processing stages easier to inspect and retry.
- **Content-based duplicate signals:** separate hashes for the original file and extracted/normalized text can identify byte-identical files as well as documents whose content is repeated despite differences in file formatting.
- **Cloud infrastructure is version-controlled:** the infrastructure is described through modular nested CloudFormation templates and containerized ECS/Fargate services rather than relying solely on manual cloud-console setup.

The repository includes a normalizer worker and related infrastructure configuration, but the current public code snapshot should be treated as a Phase I implementation. Avoid claiming a fully validated end-to-end production system or a particular duplicate-reduction rate without deployment and test results.

## 6. Technologies and tools used

| Technology | Role |
|---|---|
| Python | API, ingestion workers, parsing, hashing, and orchestration |
| FastAPI | HTTP upload and health-check endpoints |
| PyJWT | Bearer-token validation and tenant context |
| Pydantic / pydantic-settings | Configuration and typed application settings |
| Docker | Packaging the API and worker services |
| Amazon S3 | Original, extracted, normalized, Textract-output, and log storage |
| Amazon SQS | Asynchronous extraction, normalization, hashing, and bulk job queues |
| Amazon SNS | Completion notifications and Textract event integration |
| Amazon Textract | OCR and asynchronous text extraction from scanned documents and images |
| Amazon ECS | Container orchestration for API and worker services |
| AWS Fargate | Serverless container compute |
| Amazon ECR | Container image registry for deployed services |
| Amazon Aurora MySQL | Candidate metadata, ingestion status, and hash persistence |
| PyMySQL | Python connectivity to Aurora MySQL |
| AWS CloudFormation | Modular Infrastructure as Code and nested stack deployment |
| Amazon VPC, ALB, NAT, IAM | Networking, traffic routing, and access control |
| Amazon CloudWatch | Container and worker logs |
| AWS Systems Manager Parameter Store | Runtime configuration distribution |
| AWS Secrets Manager | Database credential storage |
| python-docx | DOCX parsing |
| pypdf | PDF text extraction |
| filetype | Binary file-type detection |
| pandas | CSV bulk-resume parsing |
| Python zipfile | In-memory ZIP archive processing |
| hashlib (SHA-256) | File and extracted-text hashing |

## Limitations and next steps

- Complete and verify the normalization worker's implementation and its integration with the hash queue.
- Add end-to-end integration tests covering upload, extraction, Textract callbacks, normalization, hashing, and database persistence.
- Add explicit file-size, MIME-type, archive-size, and decompression-bomb safeguards for uploads and ZIP files.
- Add idempotency controls so retried queue messages cannot create duplicate candidate records or repeated downstream events.
- Review tenant isolation in S3 object keys, database queries, queue messages, and authorization policies.
- Add dead-letter queues, retry limits, visibility-timeout guidance, and operational alarms.
- Add duplicate-detection tests for identical files, different formats containing identical text, and near-duplicate resumes.
- Measure throughput, processing latency, OCR cost, and failure rates with representative resume batches.
- Document and verify the deployed AWS environment before making production-readiness claims.
