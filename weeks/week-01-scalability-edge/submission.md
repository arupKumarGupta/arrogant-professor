# Global Unified Metric Ingestion Pipeline

## Problem Statement
Design a unified metric ingestion pipeline that is capable of
serving telemetry data like CPU usage, memory, temp. of a system globally.

## Function Requirements
1. Global Telemetry API <br> Edge Facing API for clients to send telemetry payload.
2. Metric Prioritisation <br> Prioritise metrics in Business Telemetry and Critical alerts and standard alerts.
3. Edge batching and Aggregation <br> Aggregate, compress and batch telemetry at edge POPs (Point of Presence) with 10:1 reduction
4. WAN <br> Transport batch from edge POP to central ingestion system
5. Central Queueing and Processing
6. Long term analytic storage - 30d retention
7. Deduplication of late arriving and out of order events

## Non Funtional Requirements
### Quality Attributes and SLA targets

<ol>
<li> Scale and throughput 
<ul>
<li>
Daily volume = 50B request per day 
</li>
<li>
Peak hours traffic volume is 3:1
</li>
</ul>
</li>
<li>
<ul>
<li>
Latency SLA
</li>
</ul>
<ul>
TP99 < 50ms globally
</ul>   
</li>
<li>
<ul>
Data Durability
<li>
continuous Operation during regional n/w outage (eg us-west-1 outage)
</li>
<li>
Data Retention - 30days
</li>
</ul>
</li>

<li>
Execution Contraints
<ul>
<li>No long running Daemons on Edge POPs</li>
<li>Max 50MB memory on edge workers</li>
<li>Payload Size BW: 1.2KB</li>
<li>2 hr WAN outage support</li>
<li>Ephemeral Worker - High freq cold starts and dynamic scaling without exhausting central connection pools</li>
</ul>
</li>
</ol>

## Deliverable Design Requirements

### Back of the envelop math
1. Avg and Peak ingress RPS (MBps) and Bandwidth (Gbps)
2. Edge Buffer write rate vs Immediate fwd write data
3. Central storage daily / s write volume post 10:1 compression

### HLD
End to End Mermaid diagram<br>
Geo Routing, Edge compute, Edge Buffer, Wan transport, Central queue
Stream aggregation, Analytics DB

```mermaid

```

### Schema Design
1. Transient edge layout schema
2. Central Time series DB schema

### Architectural Tradeoffs Defence
1. PACELC classification
2. Cold start and connection pool exhaustion
3. Edge memory buffer for 2 hr WAN outage
4. Pipeline dedup & handle out of order events
