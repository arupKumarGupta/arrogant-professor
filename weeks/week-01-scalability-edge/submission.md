# Global Metrics Ingestion Engine

## Overview
Engine to collect realtime telemetry - cpu, memory, custom application metric from millions of cliens running worldwide.

## Requirements

### Traffic Scale
<ul>
<li> 50billion request per day distributed globally. </li>
<li> Non uniform traffic. Assumption: standard 3:1 during peak or high traffic hours </li>
</ul>    

### Capacity Estimation
1. Client side ingestion api call < 50ms globally
2. Computing occurs at edge points-of-presence (POPs) using lightweight wasm/V8
3. Memory limit is strictly 50MB per worker execution context. No long running background processes allowed on the edge nodes
4. Zero data loss
5. Resileiancy against regional network partitions
###
4. Architecture Diagram
```mermaid
flowchart LR
        subgraph client [Clients]
            direction TB
            
            subgraph r1 [ ]
                direction LR
                User1 --> wasm_enc1[Wasm Protobuf Encoder]
            end

            subgraph r2 [ ]
                direction LR
                User2 --> wasm_enc2[Wasm Protobuf Encoder]
            end
            
            subgraph r3 [ ]
                direction LR
                User3 --> wasm_enc3[Wasm Protobuf Encoder]
            end
            
            %% Corrected: Linked row containers instead of inner nodes, broken into pairs
            r1 ~~~ r2
            r2 ~~~ r3
        end

        subgraph Gateway
            direction TB
            gw
            Lb1[Load Balancer1]
            Lb2[Load Balancer2]
            
            gw --> Lb1 & Lb2
        end

        subgraph eb [Event Broker]
            direction TB
            k1[Kafka1]
            k2[Kafka2]
            k3[Kafka3]
            
            %% Corrected: Broken down into independent pairs to avoid engine panic
            k1 ~~~ k2
            k2 ~~~ k3
        end        

        subgraph Apacheflink
            f1[Event consumer 1]
            f2[Event consumer 2]
            f1 ~~~ f2
        end


    %% Clean mapping for multi-node outputs to ensure linear tracking
    wasm_enc1 -- protobuf_raw_data ----> gw
    wasm_enc2 -- protobuf_raw_data ----> gw
    wasm_enc3 -- protobuf_raw_data ----> gw
    
    gw -- protobuf_raw_data ----> eb --> Apacheflink


    %% Clean up the border lines around your invisible user row wrappers
    style r1 fill:none,stroke:none;
    style r2 fill:none,stroke:none;
    style r3 fill:none,stroke:none;
```

5. Components
6. Data Flow
7. Database Design
8. API Design
9. Scaling Strategy
10. Reliability & Disaster Recovery
11. Security    
12. Monitoring
13. Deployment Architecture
14. Technology Stack
15. Risks & Trade-offs
16. Future Enhancements