# Week 1: Scalability, Latency & Edge Computing (High-Level Design)

## Objectives
* Deeply understand **CAP/PACELC** tradeoffs and their practical implications in edge-to-cloud architectures.
* Architect low-latency edge-native systems using lightweight compute (WASM/Cloudflare Workers) and edge databases (Turso/D1).
* Mitigate cold starts and resources constraints (e.g., 50MB memory limits per execution context).
* Design robust ingestion pipelines capable of absorbing massive traffic spikes without losing telemetry.

---

## Assignment: Global Metrics Ingestion Engine (50B Requests/Day)

### Problem Statement
You are the Lead Architect at a global SaaS infrastructure company. You need to design a **Global Metrics Ingestion Engine** that collects real-time telemetry (CPU, memory, custom application metrics) from millions of client agents running worldwide.

### Scaling Constraints & System Requirements
1. **Traffic Scale**:
   * **50 Billion requests per day** distributed globally.
   * Traffic is not uniform. Assume a standard peak-to-average ratio of **3:1** during global high-traffic hours.
2. **Latency SLA**:
   * Client-side ingestion API call p99 latency must be **< 50ms** globally.
3. **Resource Constraints at the Edge**:
   * Computing occurs at edge points-of-presence (PoPs) using lightweight WASM/V8 isolates (e.g., Cloudflare Workers). 
   * Memory limit is strictly **50MB** per worker execution context. No long-running background daemon processes allowed on the edge nodes.
4. **Data Durability & High Availability**:
   * Zero data loss tolerance for high-priority metrics (e.g., billing telemetry, critical system alerts).
   * Resilience against regional network partitions or cloud provider outages (e.g., US-East-1 goes offline).

---

## Deliverables
You must document your design in a file named `submission.md` within this directory (`weeks/week-01-scalability-edge/submission.md`). Your submission must address the following:

### 1. Back-of-the-Envelope Calculations (Showing all mathematical steps)
* **Ingress Throughput**:
  * Average and peak requests per second (RPS) handled by the global edge layer.
  * Bandwidth requirements (ingress MB/s and Gbps) assuming an average request payload size of **1.2 KB**.
* **Edge Storage/Memory footprint**:
  * What is the required rate of database writes or message queuing writes if you buffer at the edge vs forwarding immediately to an origin?
* **Origin/Central Ingestion requirements**:
  * Assuming 10:1 data compression via aggregation/batching at the edge, what is the data volume written to the central storage per second/day?

### 2. High-Level Architecture (HLD)
* Design a complete end-to-end data flow using **Mermaid.js**.
* Include the following components:
  * **Geo-routing** (Anycast DNS vs Latency-based Routing).
  * **Edge Compute Ingestion Layer** (WASM/Workers).
  * **Edge Buffer/Storage** (e.g., local queuing, durable objects, or lightweight edge database).
  * **WAN Transport Protocol** (HTTP/2, HTTP/3, or gRPC streaming for edge-to-origin data transit).
  * **Central Queue/Ingestion Buffer** (Kafka/Pulsar at origin).
  * **Batch/Stream Aggregator** (Flink/Spark).
  * **Long-term Analytics Database** (ClickHouse, Snowflake, or timeseries-optimized column-store).

### 3. Database Schema Design
* **Edge / Local schema**: If you use any transient storage at the edge, define its layout.
* **Analytics Core schema**: The schema for the central analytics store, including index keys (sorting/partition keys) optimized for querying time-series data over a 30-day retention window.

### 4. Architectural Defense & Hard Trade-offs
You must write detailed architectural answers defending your decisions:
1. **PACELC Classification**: Where does your design stand under normal operation (Latency vs Consistency) and during partitions (Availability vs Consistency)? Justify why this is correct for a metrics engine.
2. **Cold-Start & Connection Pool Exhaustion**: Workers spin up and down dynamically. How does your edge layer communicate with the central database/queues without exhausting connection pools or incurring high connection setup latencies?
3. **Data Loss Mitigation under WAN Partition**: If the WAN link between an edge PoP and the origin is severed for 2 hours, how does your system handle incoming metrics? Show the limits of your edge buffering design given the 50MB execution memory constraint.
4. **Deduplication and Out-of-Order Events**: Network jitter and client retries will cause duplicate and out-of-order metrics. At what stage of the pipeline do you handle deduplication, and how does your central analytical database handle late-arriving data?

---

## How to Submit
1. Create and write your design in [weeks/week-01-scalability-edge/submission.md](file:///Volumes/excalibur/projects/system-design-teacher/weeks/week-01-scalability-edge/submission.md).
2. Tell me: *"I have completed my submission. Please grade it."*
3. Prepare for a highly critical, merciless review of your design.

