---
layout: page
title: Projects
permalink: /projects/
---

## Machine Learning & Computer Vision

* [LoRA and BUSI — Breast Ultrasound Image Analysis]({{ site.baseurl }}/projects/lora-busi/)
  - Explores parameter-efficient fine-tuning with Low-Rank Adaptation (LoRA) for breast ultrasound image classification using the BUSI dataset and a pretrained DeiT vision transformer.
  - Includes training and evaluation workflow, classification metrics, and Grad-CAM visualizations.

* [Deforestation Detection — Satellite-Based Forest Change Monitoring]({{ site.baseurl }}/projects/deforestation-detection/)
  - An end-to-end Sentinel-2 pipeline using multispectral imagery, a four-channel U-Net, and temporal change detection.
  - Converts candidate forest-loss regions into GeoJSON polygons and visualizes them in a Streamlit/Folium dashboard.

## Cloud & Data Engineering

* [Serverless IoT Data Pipeline with AWS CloudFormation]({{ site.baseurl }}/projects/CloudFormation)
  - A modular Infrastructure as Code (IaC) deployment for IoT telemetry ingestion, S3/Firehose archiving, EventBridge event routing, and serverless querying.

* **Clinical Detection Pipeline — React Interface**
  - A React-based interface for interacting with a clinical detection pipeline.
  - Organizes pipeline inputs and outputs in a user-facing workflow for reviewing detection results.

* **Resume Ingestion and Processing Pipeline**
  - An automated workflow for ingesting resumes and extracting structured candidate information.
  - Supports downstream processing and integration with recruitment and recommendation workflows.

## Applied AI

* **AI Compliance Assistant**
  - A retrieval-augmented generation (RAG) assistant designed to answer compliance-related questions through a conversational interface.
  - Uses a language model and retrieved context to provide responses grounded in relevant compliance information.

* **Talent Matchmaker**
  - An AI-powered recruitment tool that matches candidate profiles with role requirements.
  - Combines resume information and job criteria to help surface relevant candidate-role matches.

* **Job Recommendation Pipeline**
  - A recommendation workflow that uses candidate profiles and job information to identify potentially relevant opportunities.
  - Structures candidate and job data for matching and personalized recommendations.
