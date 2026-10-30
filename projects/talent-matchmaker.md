---
layout: page
permalink: /projects/talent-matchmaker/
---

# Talent Matchmaker — Multi-Stage AI Candidate Matching and Ranking

## 1. Project overview

Talent Matchmaker is a recruitment-matching pipeline that evaluates candidate profiles against a supplied job description. The repository implements a staged workflow: a job description is parsed into a structured job profile, candidate records are loaded and normalized, candidates pass through successive filtering layers, a final assessment stage evaluates shortlisted candidates, and a scoring/ranking stage produces a final shortlist.

The orchestration code uses Claude for job-description parsing and candidate assessment, loads candidate data from DynamoDB or a normalized cache, and writes detailed results and a run summary to JSON. The design separates parsing, candidate loading, filtering, ranking, scoring, and presentation-oriented output.

**Repository:** [talentmatchmaker-](https://github.com/aditiprashant07/talentmatchmaker-)

## 2. Objectives

- Convert unstructured job descriptions into structured role requirements.
- Load and normalize candidate profiles from the candidate data source.
- Reduce a large candidate pool through successive filtering stages.
- Evaluate candidate fit using job-specific signals and AI-assisted assessment.
- Rank shortlisted candidates and produce an explainable result payload.
- Keep the pipeline modular so parsing, filtering, assessment, and scoring can be developed independently.
- Record stage timings and run summaries for operational visibility.

## 3. Were the objectives achieved?

The repository's main orchestrator implements a six-stage run: parse the job description with Claude, load normalized candidates, apply layered filtering and ranking, run a Claude assessment followed by final scoring, and write detailed JSON output plus a summary. The code includes a configurable Layer 1 shortlist target, a Layer 4 final filtering session, a final ranking layer, and a top-20 output for the frontend.

The repository also contains dedicated modules for job parsing, candidate loading, domain/title/location filtering, embeddings, matching, scoring, and tests. This demonstrates an implemented multi-stage matching workflow. It does not, by itself, establish real-world hiring accuracy, fairness, or improved recruiter outcomes; those require representative labelled data, human review, and measured evaluation.

## 4. Concepts learned and applied

- Parsing unstructured job descriptions into structured job profiles.
- Candidate-profile normalization and data ingestion.
- Multi-stage retrieval and filtering to narrow large candidate pools.
- Domain, job-title, location, seniority, skill, experience, and work-mode signals.
- Semantic embeddings and similarity-based matching.
- Hybrid ranking that combines deterministic filters with AI-assisted assessment.
- Claude/LLM integration for structured extraction and candidate assessment.
- Layered scoring, confidence estimation, and ranking.
- Caching and parallel candidate loading from DynamoDB.
- Pipeline orchestration, timing instrumentation, and structured JSON outputs.
- Explainability through intermediate match information and assessment records.
- Testing modular matching components.

## 5. Approach and what is different about it

### Approach

1. **Parse the job description.** The pipeline sends the supplied JD to a Claude-backed parser to produce a structured job profile.
2. **Load candidate data.** Candidate records are loaded from DynamoDB using parallel segmented scans or from a normalized cache; a force-refresh option can bypass cache freshness.
3. **Apply early filtering layers.** The matching engine applies staged filters to reduce the candidate set using job-relevant profile signals.
4. **Run final filtering and ranking.** A dedicated Layer 4 session performs final filtering before the ranking layer prepares candidates for deeper assessment.
5. **Perform AI-assisted assessment.** The shortlisted candidates are passed through a Claude assessment workflow, with multiple assessment dimensions feeding a final scoring engine.
6. **Produce a shortlist and traceable outputs.** The orchestrator writes the job profile, original JD, top-20 candidates, assessments, candidate-load metadata, stage counts, and timing information to JSON files.

### What makes this approach distinctive?

- **Progressive narrowing:** candidates are reduced in stages rather than sending every profile directly to an expensive LLM assessment.
- **Hybrid matching:** deterministic filters and ranking logic are combined with semantic/AI-assisted evaluation.
- **Separation of responsibilities:** JD parsing, candidate loading, filtering, assessment, final scoring, and output generation are modular.
- **Operational awareness:** the run records stage timings, candidate counts, cache source, and final shortlist size.
- **Configurable candidate routing:** the Layer 1 target can be adjusted and is clamped to configured limits, allowing control over the breadth of the candidate pool.

The architecture is designed to make matching more structured and inspectable. It should not be described as unbiased or objectively accurate without a documented evaluation across representative candidate groups and human-reviewed outcomes.

## 6. Technologies and tools used

| Technology | Role |
|---|---|
| Python | Main pipeline implementation and orchestration |
| Claude / Anthropic API | Job-description parsing and candidate assessment |
| AWS DynamoDB | Candidate data source |
| JSON | Normalized candidate data, detailed results, and run summaries |
| Sentence Transformers | Semantic embeddings in matching components |
| NumPy / vector operations | Similarity and scoring calculations where used |
| Flask | API/service components in the repository |
| Redis and Celery | Async matching workflow dependencies |
| pytest | Automated testing |
| python-dotenv | Environment configuration |

## Limitations and next steps

- Evaluate ranking quality with recruiter-labelled relevance judgements and metrics such as Precision@K, Recall@K, and NDCG.
- Audit outcomes across relevant demographic groups and document fairness safeguards.
- Add human review and feedback loops for shortlist decisions.
- Document the eight assessment dimensions and scoring weights in a stable public specification.
- Add reproducible benchmark datasets and run reports.
- Treat the system as decision support; do not automate hiring decisions without accountable human oversight.
