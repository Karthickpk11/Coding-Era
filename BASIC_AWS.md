## Architecture depth, trade-offs, real-world problem solving, and cost/security optimization.

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

---

## Route 53?    
Amazon Route 53 is AWS’s `scalable Domain Name System (DNS) and traffic routing service`. It translates human-friendly domain names (like example.com) into IP addresses and intelligently routes users to the right backend.

What Route 53 Actually Does

When a user types a domain:

1. Route 53 receives the DNS query
2. Decides **where to send the request**
3. Returns the best IP endpoint (server, load balancer, region)

It’s called **“Route 53”** because:

* “Route” → traffic routing
* “53” → standard DNS port (UDP/TCP 53)

# Core Features

1. DNS Management

    * Register domains
    * Create DNS records (A, AAAA, CNAME, MX, etc.)
    * Example: Point `app.example.com` → EC2 / Load Balancer

2. Intelligent Routing Policies

    Simple Routing
    
    * One resource → one domain
    * No health checks
    
    Weighted Routing
    
    * Split traffic (e.g., 70% / 30%)
    * Useful for A/B testing
    
    Latency-Based Routing
    
    * Sends users to **closest region**
    * Example: India users → Mumbai region
    
    Health Check + Failover Routing
    
    * Detects unhealthy endpoints
    * Automatically switches to backup (used in Active-Passive)
    
    Geolocation Routing
    
    * Routes based on **user location**
    * Example: EU users → EU servers (for compliance)
    
    Multi-Value Routing
    
    * Returns multiple IPs
    * Basic load balancing with health checks

3. Health Checks

    Route 53 can monitor:
    
    * HTTP/HTTPS endpoints
    * TCP ports
    * CloudWatch alarms
    
    If a service fails → traffic is rerouted automatically.

Integration with AWS

Works seamlessly with:

* **Elastic Load Balancing** – Route to ALB/NLB
* **Amazon CloudFront** – Global content delivery
* **Amazon S3** – Static website hosting
* **Amazon EC2** – Application servers

---

Route 53 in Multi-Region Architectures

    Active-Active
    
    * Uses **Latency-based routing**
    * Sends users to nearest healthy region
    * Can combine with weighted routing
    
    Active-Passive
    
    * Uses **Failover routing**
    * Primary region → Secondary only on failure

Example Scenarios

    Global Web App
    
    * India users → Mumbai
    * US users → Virginia
      👉 Achieved using latency routing
    
     🚨 Disaster Recovery
    
    * Primary app in Region A
    * Backup in Region B
      👉 Failover routing switches automatically
    
    🧪 A/B Testing
    
    * Version A → 80% traffic
    * Version B → 20% traffic
      👉 Weighted routing

 Why Use Route 53?

    * Highly available (global AWS infrastructure)
    * Low latency DNS resolution
    * Built-in health checks
    * Fine-grained traffic control

---
