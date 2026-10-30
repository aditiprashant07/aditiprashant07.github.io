---
layout: page
permalink: /projects/ai-compliance/
---

# AI Compliance Assistant — RAG-Based Compliance Guidance and Assessment

## 1. Project overview

This project is an AI-assisted compliance platform designed to help users explore compliance requirements, ask questions against a curated knowledge base, and complete structured assessments. The repository contains an AWS-oriented backend with document ingestion, assessment, scoring, PDF/reporting, enquiry, alert, and email-worker components.

The data model supports source documents and embedded knowledge chunks, conversational sessions, user-uploaded document chunks, compliance assessments, generated assessment reports, audit events, and usage tracking. A retrieval-augmented generation (RAG) approach grounds responses in ingested material rather than relying only on a language model's parametric knowledge.

**Repository:** [aicompliance](https://github.com/aditiprashant07/aicompliance)

## 2. Objectives

- Provide a conversational interface for compliance-related questions.
- Ingest compliance documents and transform them into searchable knowledge chunks.
- Retrieve relevant context to ground generated responses.
- Support structured compliance assessments and scoring.
- Generate assessment reports and store report references.
- Support user sessions and conversation history across supported channels.
- Maintain audit events and usage controls for operational governance.

## 3. Were the objectives achieved?

The repository contains backend modules and database structures for the main workflow: document sources and vectorized knowledge chunks, user document chunks, conversations, assessments, assessment reports, audit events, and channel usage tracking. The backend requirements also identify integrations for AWS services, OpenAI, PostgreSQL, Redis, PDF processing, and Twilio.

This supports the conclusion that substantial backend components and data structures for ingestion, retrieval, assessment, reporting, and messaging have been implemented. The repository by itself does not provide a complete independent evaluation of answer faithfulness, compliance accuracy, assessment validity, or production uptime. It should be presented as an AI-assisted compliance workflow, not as a substitute for qualified legal or compliance advice.

## 4. Concepts learned and applied

- Retrieval-Augmented Generation (RAG) and grounding language-model responses in retrieved sources.
- Document ingestion, text extraction, chunking, metadata, and embedding generation.
- Vector similarity search using PostgreSQL with the pgvector extension.
- Relational schema design for users, sessions, source documents, assessments, reports, and audit events.
- JSONB for flexible conversation and assessment payloads.
- Session management and conversation retention.
- Multi-channel conversational workflows and messaging integration.
- Assessment design, scoring, and report generation.
- AWS serverless architecture and service integration.
- Redis-backed caching and usage tracking.
- Audit logging, data retention, and token-usage controls.
- PDF generation and document handling.

## 5. Approach and what is different about it

### Approach

1. **Ingest source material.** A document-ingestion component processes compliance material and records source metadata.
2. **Create a searchable knowledge layer.** Documents are split into chunks, summarized/tagged, embedded, and stored in PostgreSQL using pgvector for semantic retrieval.
3. **Handle user questions.** Conversation/session components maintain context while retrieval supplies relevant knowledge chunks for a grounded response.
4. **Support user-provided documents.** The schema includes session-scoped user document chunks with expiry metadata, separating temporary user material from the shared knowledge base.
5. **Run structured assessments.** Assessment components capture answers and persist status, posture band, and score.
6. **Generate reports.** Assessment report records track PDF output stored in S3 and email-delivery status.
7. **Manage operations and governance.** Audit events and WhatsApp usage tables support traceability and usage limits; Redis provides a fast cache while PostgreSQL retains durable usage records.

### What makes this approach distinctive?

- **Knowledge-grounded answers:** retrieval is designed to connect generated responses to stored compliance source material.
- **A combined assistant and assessment workflow:** the platform goes beyond question answering by including structured assessments, scoring, and report generation.
- **Shared and session-scoped knowledge:** the schema distinguishes centrally ingested knowledge from temporary user-document chunks.
- **Operational governance:** the backend includes audit-event storage, conversation retention metadata, and cumulative WhatsApp token-usage controls.
- **Multi-channel direction:** the dependency set and schema include Twilio and channel-aware conversations, supporting messaging integration alongside web sessions.

These design features support traceability and workflow management, but do not by themselves prove regulatory compliance or guarantee that generated advice is correct.

## 6. Technologies and tools used

| Technology | Role |
|---|---|
| Python | Backend and serverless function implementation |
| AWS Lambda | Serverless backend handlers and workers |
| Amazon S3 | Document/report storage and generated PDF locations |
| Amazon Bedrock | Model integration in the AWS-based assistant workflow |
| OpenAI SDK | Model/API integration in backend components |
| PostgreSQL | Persistent relational data store |
| pgvector | Vector embeddings and similarity search |
| Redis | Caching and fast usage/session lookups |
| boto3 / botocore | AWS SDK integration |
| Twilio | WhatsApp/messaging channel integration |
| pypdf | PDF text extraction and document processing |
| WeasyPrint | PDF/report generation |
| SQL | Schema, indexes, vector extension, and data persistence |

## Limitations and next steps

- Add a reproducible evaluation set for retrieval quality, groundedness, answer correctness, and citation/source coverage.
- Define human review and escalation for ambiguous or high-impact compliance questions.
- Document the exact model and prompt versions used in each deployment.
- Add security, privacy, and retention documentation for uploaded documents and conversation data.
- Test access controls, tenant isolation, token limits, and failure/retry paths.
- Make clear to users that the assistant supports compliance research and assessment workflows but does not replace professional legal or compliance review.
