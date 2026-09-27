# International Telecom — Global Landing Zone Architecture

An interactive visualization of a global cloud architecture I designed and implemented. It shows how a private network connects distributed business operations while keeping regional security boundaries intact.

## Business Problem

A global organization needs workloads in the Americas, Europe, and Asia-Pacific to communicate without exposing its internal systems directly to the public internet.

The design also needs to account for an important operational reality: not every region requires the same type of connectivity. Some workloads need routine private application access, while others require a more tightly controlled path with explicitly permitted traffic.

The objective was therefore to create an architecture that provides:

- Private connectivity between geographically distributed environments
- A centralized European hub with regional spokes
- Different connectivity methods based on regional requirements
- Explicit traffic boundaries between environments
- No direct public ingress to application workloads
- A design that can be understood and validated visually

## Architecture Approach

I designed the environment as a **hub-and-spoke global network** using three Google Cloud VPCs.

**Europe** serves as the central hub. The **Americas** and **Asia-Pacific** environments operate as spokes, with each connectivity path selected according to its requirements rather than applying one networking method everywhere.

### Americas → Europe

The Americas environment contains workloads in both `us-central1` and `us-east1`.

Because both environments operate within Google Cloud and require private application connectivity, the Americas VPC connects directly to the European hub through **VPC Network Peering**.

The implemented application path allows the Americas environment to reach the European workload over **HTTP (TCP 80)** while keeping the communication on private network paths.

### Europe → Asia-Pacific

The Asia-Pacific workload resides in `asia-southeast1`.

Instead of extending the same peering model to this environment, I used a **Classic IPsec VPN** between Asia-Pacific and the European hub. This creates a distinct encrypted connectivity boundary and allows traffic between the environments to be controlled explicitly.

The implemented management path permits **Europe → Asia-Pacific RDP (TCP 3389)**, while selected Asia-Pacific initiated traffic toward the European environment is denied.

## Security Model

The architecture follows a private-by-default approach.

Application VM interfaces do not require public IP addresses for inbound access. The European environment uses Cloud NAT where outbound internet access is required for tasks such as software installation and updates.

Firewall policy is based on required traffic flows rather than broad regional access. The design intentionally avoids direct spoke-to-spoke connectivity between the Americas and Asia-Pacific environments.

This results in a simple trust model:

**Americas → European Hub ← Asia-Pacific**

Each spoke has a defined relationship with the hub without automatically gaining connectivity to the other spoke.

## Architecture at a Glance

| Environment | Region | Role | Connectivity |
| --- | --- | --- | --- |
| Americas | `us-central1`, `us-east1` | Spoke | VPC Network Peering |
| Europe | `europe-west1` | Hub | Central connectivity point |
| Asia-Pacific | `asia-southeast1` | Spoke | Classic IPsec VPN |

The interactive architecture visualization maps these cloud regions to their approximate geographic locations and demonstrates the intended traffic flows between them.

## Why This Design

The goal was not simply to connect three networks. It was to choose connectivity and security controls based on the role of each environment.

The resulting architecture demonstrates several cloud networking principles in a single design:

- Hub-and-spoke network organization
- Multi-region private connectivity
- VPC Network Peering
- IPsec VPN connectivity
- Controlled ingress and egress
- Regional workload isolation
- Non-transitive spoke relationships
- Infrastructure designed around explicit business traffic requirements

## Interactive Visualization

This repository includes an interactive browser-based representation of the architecture.

The visualization is intentionally presented at a higher level than the underlying infrastructure implementation. It allows a reviewer to quickly understand the global topology, regional placement, connectivity methods, and security boundaries without requiring them to first interpret Terraform or individual cloud resources.

Use the **Explore Traffic Flows** controls to examine the primary application paths and security boundaries.

## Implementation

The underlying cloud environment was implemented with infrastructure as code on Google Cloud.

This public visualization is limited to architecture and design intent. It does not include environment-specific implementation details, credentials, or other sensitive infrastructure configuration.

---

**Project focus:** Google Cloud networking · Global architecture · Hub-and-spoke design · VPC Peering · IPsec VPN · Private connectivity · Network security
