## Architecture depth, trade-offs, real-world problem solving, leadership, and cost/security optimization.

# 1. Architecture & Design (Advanced)  
Design a globally distributed, highly available system on AWS.

Expectations:  
Multi-region (Active-Active vs Active-Passive)
Route 53 latency-based routing
Data replication (Aurora Global DB / DynamoDB Global Tables)
Failure scenarios

**Active-Active vs Active-Passive** 

Multi-region architecture in Amazon Web Services is about running your application across two or more AWS regions for high availability, disaster recovery, and low latency. The two most common patterns are Active-Active and Active-Passive.

What is a Multi-Region Setup?  
AWS regions (like Mumbai, Singapore, etc.) are geographically separate. If one region fails, another can continue serving users.

**1. Active-Active Architecture**

Both regions are live and serving traffic simultaneously.

How it works

    Users are routed to the nearest or healthiest region
    Both regions process requests
    Data is replicated between regions in near real-time
    
🛠 Key AWS Services

    Amazon Route 53 – Latency-based / health check routing
    Amazon CloudFront – Global edge delivery
    Amazon DynamoDB (Global Tables) – Multi-region DB
    Amazon Aurora (Global Database) – Cross-region replication
    
✅ Pros

    High availability (no downtime even if one region fails)
    Low latency (users hit nearest region)
    Load balancing across regions
    
❌ Cons

    Complex (data consistency, conflict resolution)
    Expensive (both regions fully active)
    
📌 Best for

    Global apps (e.g., streaming, gaming)
    Mission-critical systems requiring near-zero downtime

**2. Active-Passive Architecture**

One region is active; the other is on standby.

How it works

Primary region handles all traffic
Secondary region is idle or partially active
Failover happens only if primary fails

🛠 Key AWS Services

    Amazon Route 53 – Failover routing
    AWS Elastic Disaster Recovery – Replication & failover
    Amazon S3 – Cross-region replication (CRR)
    Amazon RDS – Cross-region read replicas

✅ Pros

    Simpler architecture
    Lower cost (standby resources minimized)
    Easier data consistency

❌ Cons

    Failover delay (seconds to minutes)
    Secondary region underutilized
    Potential cold-start issues

📌 Best for

    Disaster recovery setups
    Cost-sensitive applications
    Apps that tolerate brief downtime
