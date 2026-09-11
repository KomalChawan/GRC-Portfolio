# PCI DSS Network Segmentation & Scope Determination

## Project Overview

This project assesses the network segmentation and PCI DSS scope of a simulated e-commerce payment environment, MidTech.

The assessment examines whether MidTech can demonstrate that its Cardholder Data Environment (CDE) is effectively isolated from other network zones, or whether connected systems and security dependencies extend PCI DSS scope beyond the claimed CDE boundary.

The project analyses:

- Cardholder data flows
- CDE scope
- Connected and security-impacting systems
- Trust relationships
- Network segmentation
- Evidence required to validate scope claims
- Key risks and recommendations

## Core Assessment Question

> Can MidTech demonstrate that its network segmentation effectively isolates the Cardholder Data Environment (CDE), or do connections and security dependencies extend PCI DSS scope beyond the claimed CDE boundary?

## Business Scenario

MidTech is a simulated e-commerce organisation that processes payment card transactions. Its environment includes public-facing web systems, corporate infrastructure, a dedicated Cardholder Data Environment (CDE), and management services supporting monitoring, backup, and patch management.

MidTech claims that network segmentation isolates its CDE from other parts of the environment. However, systems outside the claimed CDE may communicate with or support the security and operation of CDE systems.

This assessment examines these connections and dependencies to determine whether MidTech's claimed CDE boundary and PCI DSS scope can be supported by architecture, configuration, and security evidence.

## Assessment Approach

The assessment follows an evidence-based approach to evaluate MidTech's claimed PCI DSS scope:

1. Review the payment environment and network architecture.
2. Map the flow of cardholder data through the environment.
3. Identify the systems that form the claimed CDE.
4. Identify systems connected to or capable of affecting the security of the CDE.
5. Analyse trust relationships and boundary-crossing dependencies.
6. Assess whether network segmentation effectively restricts unnecessary access to the CDE.
7. Identify the evidence required to support segmentation and scope claims.
8. Document findings, risks, recommendations, and the final scope determination.

## Deliverables

This project includes:

- **Scope Determination Report** — Detailed assessment of the CDE, connected systems, trust relationships, segmentation, findings, and final scope determination.
- **Network Architecture Diagram** — Visual representation of MidTech's network zones and key systems.
- **Cardholder Data Flow Diagram** — Visual representation of how cardholder data moves through the payment environment.
- **Evidence Index** — Evidence required to validate segmentation, access, data flows, and other scope-related claims.




