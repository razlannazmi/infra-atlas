# Observability Tool Candidates

Shortlist of tools being considered for the organization's observability, DevOps, and QA stack. Organized by category. Open-source tools only — no paid/commercial-licensed options.

## Instrumentation
- **OpenTelemetry** — vendor-neutral instrumentation SDK/collector for metrics, logs, and traces
  - *Alternative:* **Elastic APM Agent** — open-source instrumentation agent

## Monitoring & Alerting
- **Nagios** — infrastructure/service monitoring and alerting
- **Zabbix** — infrastructure/network monitoring, metrics collection, and alerting
- **Alertmanager** — deduplicates, groups, and routes alerts from Prometheus/VictoriaMetrics to notification channels

## Visualization & Dashboards
- **Grafana** — dashboarding and visualization layer for metrics, logs, and traces
  - *Alternatives:* **OpenSearch Dashboards** — open-source fork of Kibana; **Metabase** — general-purpose BI/dashboarding tool

## Metrics & Time-Series Storage
- **Prometheus** — metrics collection, scraping, and alerting toolkit
  - *Alternative:* **Telegraf** — plugin-driven metrics collection agent
- **VictoriaMetrics** — time-series database for metrics storage and querying (Prometheus-compatible)
  - *Alternatives:* **Thanos** — Prometheus long-term storage & global query layer; **Cortex** — horizontally scalable, multi-tenant Prometheus storage
- **InfluxDB** — time-series database with tag-based data model, Telegraf ingestion ecosystem
  - *Alternatives:* **TimescaleDB** — Postgres-based time-series database; **QuestDB** — high-performance time-series database with SQL

## GPU Metrics
- **DCGM Exporter** — NVIDIA Data Center GPU Manager exporter; exposes per-GPU utilization, VRAM, temperature, ECC, and power metrics as a Prometheus endpoint

## Logs
- **VictoriaLogs** — log storage and querying (VictoriaMetrics ecosystem)
  - *Alternatives:* **Loki** — log aggregation system designed to work with Grafana; **OpenSearch** — open-source fork of Elasticsearch (log storage & full-text search)

## Traces
- **VictoriaTraces** — distributed tracing storage and querying (VictoriaMetrics ecosystem)
  - *Alternatives:* **Jaeger** — distributed tracing platform; **Tempo** — Grafana's high-scale distributed tracing backend

## AI/LLM Observability & QA
- **DeepEval** — evaluation framework for testing LLM outputs (unit testing for LLMs)
  - *Alternatives:* **Ragas** — evaluation framework for RAG/LLM pipelines; **Promptfoo** — LLM prompt testing and evaluation
- **Langfuse** — LLM observability and tracing platform (traces, prompt management, evaluation)
  - *Alternatives:* **Arize Phoenix** — LLM observability and tracing platform; **Helicone** — LLM observability and request logging (self-hostable)

## ML Experiment Tracking & Model Management
- **MLflow** — experiment tracking, model registry, and ML lifecycle management
  - *Alternatives:* **DVC** — data/model versioning and pipeline tracking; **ClearML** — experiment tracking and ML lifecycle management (self-hostable)

## Performance & Load Testing
- **Gatling** — load and performance testing tool
  - *Alternatives:* **k6** — developer-centric load testing tool; **Locust** — Python-based distributed load testing

## Synthetic & Uptime Monitoring
- **Gatus** — self-hosted health dashboard and status page; runs configurable HTTP/TCP/DNS/ICMP checks and alerts on failures
  - *Alternative:* **Uptime Kuma** — self-hosted uptime/status monitoring with alerting; **Blackbox Exporter** — Prometheus-based endpoint probing (HTTP/TCP/ICMP/DNS)
