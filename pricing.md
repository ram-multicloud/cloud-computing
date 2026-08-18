# Azure VM vs AWS EC2 — Pricing Plans in Detail (2026)

> Pricing changes frequently and varies by region, instance/VM family, and OS. Treat the figures below as directional; always confirm with the official calculators before budgeting:
> - Azure Pricing Calculator: https://azure.microsoft.com/pricing/calculator
> - AWS Pricing Calculator: https://calculator.aws

---

## 1. Azure Virtual Machines — Pricing Models

### 1.1 Pay-As-You-Go (PAYG)
- No commitment, billed per second/hour of usage.
- Highest per-hour rate — used as the baseline against which all other discounts are measured.
- Example list prices (East US, Linux): <cite index="11-1">a B1s burstable VM runs about $7.59/month and a D2s v3 runs about $70.08/month, with typical production environments in the $200–$5,000+/month range</cite>.
- <cite index="9-1">Compute is generally priced from roughly $0.02/vCPU-hour for burstable B-series up to roughly $0.065/vCPU-hour for memory-optimized E-series</cite>, plus separate charges for storage and data egress.
- New accounts typically get a credit and some free-tier usage for the first 12 months — check the current offer on Azure's site, as terms change.

### 1.2 Reserved VM Instances (RIs)
- Commit to a specific VM size/region for a **1-year or 3-year term** in exchange for a lower fixed hourly rate.
- <cite index="3-1">Reserved Instances guarantee a fixed hourly price for the contract length and reserve capacity for that period; usage beyond the reserved capacity is billed separately at standard rates</cite>.
- <cite index="3-1">Savings can reach up to 72% off standard rates on Linux, and up to 80% when combined with Windows VMs plus Azure Hybrid Benefit</cite>.
- <cite index="9-1">Roughly speaking, a 1-year RI saves about 41% and a 3-year RI saves about 62% versus PAYG</cite>, though exact savings depend on VM family and region.
- Payment options: all upfront, monthly (no extra cost for monthly billing).
- Best for stable, predictable workloads that run continuously (e.g., production databases, always-on app servers).
- **Important 2026 change:** <cite index="8-1">starting July 1, 2026, Azure Reserved VM Instances for select VM series are no longer available for new purchase or renewal — existing reservations keep running through their term, but renewals for the affected series won't be offered</cite>, so Microsoft is nudging customers toward Savings Plans for those series.

### 1.3 Azure Savings Plans (for Compute)
- A dollar-per-hour spend commitment (not tied to a specific VM size/region) for **1 or 3 years**.
- <cite index="6-1">You commit to spending at least a set dollar amount per hour on eligible compute, and Azure automatically applies discounted rates to that usage — covering Azure VMs, App Service Premium v3, Container Instances, and Azure Functions Premium</cite>.
- <cite index="5-1">Available as a 1-year or 3-year term for compute; a separate database savings plan covers select database services at a 1-year term</cite>.
- <cite index="2-1">Savings are estimated between roughly 11% and 65%, with the 65% figure based on a 3-year plan for a specific large Linux VM</cite> — most real-world production workloads land more modestly.
- <cite index="6-1">Typical production VM workloads realize discounts in the 30–50% range</cite> in practice.
- More flexible than RIs (auto-applies across VM families/regions) but generally slightly shallower max discount than RIs.
- Does **not** cover: <cite index="6-1">Azure SQL Database, Cosmos DB, storage, networking, or other non-compute services</cite> — those need their own reservations or database savings plans.

### 1.4 Azure Hybrid Benefit
- Lets customers with existing on-prem Windows Server / SQL Server licenses (with Software Assurance) apply those licenses to Azure VMs instead of paying the Windows surcharge.
- <cite index="9-1">This can cut Windows VM costs by roughly 40%</cite> and stacks with Reserved Instances or Savings Plans for additional savings.

### 1.5 Spot VMs
- Access unused Azure compute capacity at a steep discount, with the risk that Azure can reclaim the capacity with short notice.
- Best for interruption-tolerant batch jobs, dev/test, and stateless workloads.

### 1.6 Hidden / Additional Costs to Budget For
- <cite index="10-1">A VM's list price is not the whole bill — disks, a public IP, and premium OS images are billed separately from compute</cite>.
- <cite index="11-1">Common additional costs include data egress (~$0.087/GB), Premium SSD managed disk storage (~$0.075/GB/month), Load Balancer fees, Azure Monitor log ingestion (~$2.76/GB), and static IP charges</cite>.
- <cite index="11-1">Support plans add roughly $100–$1,000+/month depending on tier</cite>.

### Azure Pricing Model Summary

| Model | Commitment | Typical Max Savings | Flexibility | Best For |
|---|---|---|---|---|
| Pay-As-You-Go | None | 0% (baseline) | Highest | Unpredictable/short-term workloads |
| Savings Plan (Compute) | 1 or 3 yr, $/hr spend | ~65% (up to) | High (auto-applies across VM types/regions) | Mixed or evolving workloads |
| Reserved VM Instance | 1 or 3 yr, specific VM+region | ~72% (up to 80% w/ Hybrid Benefit) | Low (fixed VM family/region) | Stable, predictable, always-on workloads |
| Spot VM | None | Very high (variable) | Low (can be reclaimed) | Fault-tolerant batch/dev-test |

---

## 2. AWS EC2 — Pricing Models

### 2.1 On-Demand
- <cite index="13-1">No commitment and the highest per-hour cost of the four EC2 pricing models</cite>.
- Billed per second (Linux) with a 60-second minimum, ideal for unpredictable or short-lived workloads and initial testing.

### 2.2 Reserved Instances (RIs)
- <cite index="13-1">A 1–3 year term commitment that can save up to 72% versus On-Demand</cite>.
- Locks in a specific instance configuration (family, size, region, sometimes AZ).
- <cite index="12-1">AWS itself now generally recommends Savings Plans over RIs for most use cases</cite>, but RIs still make sense <cite index="12-1">when you need a capacity reservation in a specific Availability Zone (which Savings Plans don't provide), when your workload configuration is very stable, or if you want access to the Reserved Instance Marketplace for resale</cite>.
- Payment options: All Upfront, Partial Upfront, No Upfront — All Upfront typically yields the deepest discount.
- <cite index="13-1">A common strategic split is to use Reserved Instances specifically for the database tier (RDS, ElastiCache, Redshift, OpenSearch), since that tends to be the most stable workload class and benefits most from the deeper RI discount</cite>.

### 2.3 Savings Plans
Two flavors:
- **Compute Savings Plans** — <cite index="13-1">apply automatically to EC2 usage regardless of instance family, size, Availability Zone, region, operating system, or tenancy, and also apply to Fargate</cite>. <cite index="12-1">Savings up to ~66%.</cite>
- **EC2 Instance Savings Plans** — narrower (tied to an instance family in a region) but deeper discount, <cite index="12-1">up to ~72%</cite>.
- <cite index="13-1">The general guidance is to use Compute Savings Plans for an EC2/Lambda baseline that evolves over time</cite>, reserving RIs for the stable database tier — the two can be combined without conflict.
- Both are 1-year or 3-year, dollar-per-hour commitments (like Azure Savings Plans), and both can run alongside RIs.

### 2.4 Spot Instances
- <cite index="12-1">Spare EC2 capacity offered at up to 90% off On-Demand pricing</cite>.
- <cite index="12-1">Prices adjust gradually based on long-term supply and demand trends, and instances receive a 2-minute interruption notice before AWS reclaims the capacity</cite>.
- <cite index="12-1">Best suited to fault-tolerant, interruption-safe workloads: batch processing, data pipelines, CI/CD builds, checkpointed ML training, and stateless web workloads</cite>.

### 2.5 Dedicated Hosts
- <cite index="13-1">Single-tenant physical hardware, typically used for compliance requirements or bring-your-own-license (BYOL) software licensing</cite>.
- Priced at a premium versus shared-tenancy On-Demand, but can offset software licensing costs tied to physical cores/sockets.

### 2.6 Realistic Blended Savings
- <cite index="13-1">Most production environments run a mix of Reserved/Savings Plans for steady-state workloads, On-Demand for variable traffic, and Spot for fault-tolerant batch jobs, landing around 35–50% blended savings without changing any application code</cite>.
- <cite index="14-1">Coverage — the percentage of total compute usage billed under discounted pricing rather than On-Demand rates — is the primary driver of realized savings, more so than which specific model is chosen</cite>. Two teams with identical Savings Plans but very different coverage levels (e.g., 80% vs. 40%) end up with very different effective bills.

### AWS Pricing Model Summary

| Model | Commitment | Typical Max Savings | Flexibility | Best For |
|---|---|---|---|---|
| On-Demand | None | 0% (baseline) | Highest | Unpredictable/short-term workloads |
| Compute Savings Plan | 1 or 3 yr, $/hr spend | ~66% (up to) | High (any instance family/region/OS, + Fargate) | Evolving EC2/Lambda baseline |
| EC2 Instance Savings Plan | 1 or 3 yr, $/hr spend | ~72% (up to) | Medium (tied to instance family + region) | Stable EC2 usage within a family |
| Reserved Instance | 1 or 3 yr, specific config | ~72% (up to) | Low (fixed config; AZ reservation possible) | Stable DB tier, AZ capacity guarantees |
| Spot Instance | None | Up to ~90% | Low (2-min interruption notice) | Fault-tolerant batch/CI/ML training |
| Dedicated Host | Varies | N/A (premium pricing) | Low | Compliance, BYOL licensing |

---

## 3. Azure vs AWS — Side-by-Side Comparison

| Dimension | Azure | AWS |
|---|---|---|
| No-commitment tier | Pay-As-You-Go | On-Demand |
| Flexible spend-commitment plan | Savings Plan for Compute (1/3 yr) | Compute Savings Plan (1/3 yr) |
| Instance-family-specific commitment | Reserved VM Instance (1/3 yr) | EC2 Instance Savings Plan / Reserved Instance (1/3 yr) |
| Max advertised discount (compute) | ~65–72% (up to 80% w/ Hybrid Benefit) | ~66–72% (RIs/Savings Plans), up to 90% (Spot) |
| Spare-capacity discount tier | Spot VMs | Spot Instances |
| License mobility | Azure Hybrid Benefit (Windows/SQL Server) | Dedicated Hosts (BYOL) |
| 2026 note | RIs for select VM series being phased out for new purchase/renewal starting July 1, 2026 — Savings Plans positioned as the successor | AWS actively recommends Savings Plans over RIs as the default for most workloads |

---

## 4. Practical Recommendations

- **Unpredictable or short-term workloads:** stay on PAYG / On-Demand until usage patterns stabilize.
- **Steady, always-on production workloads:** commit via Savings Plan first (flexible), and layer Reserved Instances only where you're confident the exact VM family/region won't change (e.g., a fixed database tier).
- **Batch, CI/CD, checkpointed ML training:** use Spot VMs / Spot Instances for the deepest discounts, designed around interruption.
- **Existing Windows Server/SQL Server licenses:** apply Azure Hybrid Benefit before comparing raw list prices — it materially changes the Azure vs AWS math.
- **Always model the "hidden" costs** — storage, egress, load balancers, monitoring/log ingestion, and support plans — since compute list price alone understates the real bill on both platforms.
- **Re-check current figures before committing budget:** use the Azure Pricing Calculator and AWS Pricing Calculator directly, since published discount percentages and RI/Savings Plan availability (e.g., Azure's July 2026 RI phase-out for select series) change over time.
