# PCI DSS Network Segmentation & Scope Determination

## Project Overview

This project assesses the network segmentation and PCI DSS scope of a simulated e-commerce payment environment (MidTech).

The assessment examines whether the Cardholder Data Environment (CDE) is effectively isolated from the Corporate and Management networks. It identifies systems connected to or capable of affecting the security of the CDE, analyses trust relationships and data flows, and determines the evidence required to support the organisation's segmentation and scope claims.

The assessment focuses on the following question:

> Can MidTech demonstrate that its network segmentation effectively isolates the CDE, or do connections and security dependencies extend PCI DSS scope beyond the claimed CDE boundary?

## Business Scenario

MidTech is a simulated e-commerce organisation that processes payment card transactions. Its environment is divided into multiple network zones, including a public-facing DMZ, Corporate Network, Management Network, and a dedicated Cardholder Data Environment (CDE).

MidTech considers the CDE to be isolated from the rest of the environment through network segmentation. However, several systems outside the CDE may communicate with or support the security and operation of CDE systems, including identity, administrative access, logging, backup, and patch management services.

The purpose of this assessment is to examine these connections and determine whether MidTech's claimed CDE boundary is adequately supported.

## Assessment Objective

The objective of this assessment is to:

- Identify the systems and components that form the Cardholder Data Environment (CDE).
- Identify systems outside the CDE that communicate with or can affect the security of the CDE.
- Analyse trust relationships and boundary-crossing dependencies.
- Assess whether network segmentation can effectively limit PCI DSS scope.
- Identify the evidence required to validate segmentation and scope claims.
- Document key findings, risks, recommendations, and residual risk.

## PCI DSS Concepts Applied

### Payment Card Industry Data Security Standard (PCI DSS)

PCI DSS is a security standard designed to protect payment card data. This assessment applies PCI DSS concepts to determine which systems and connections are relevant to the security of the payment environment.

### Cardholder Data (CHD)

Cardholder Data includes payment card information such as the Primary Account Number (PAN). Identifying where CHD is stored, processed, or transmitted is the starting point for determining PCI DSS scope.

### Cardholder Data Environment (CDE)

The CDE consists of the people, processes, and technologies involved in storing, processing, or transmitting cardholder data, or otherwise supporting the security of those activities.

### PCI DSS Scope

PCI DSS scope is not limited only to systems that directly store or process cardholder data. Systems connected to the CDE or capable of affecting its security must also be considered during scope determination.

### Network Segmentation

Network segmentation is used to separate the CDE from other parts of the network and restrict unnecessary communication. Effective segmentation can help reduce PCI DSS scope, but the organisation must be able to demonstrate that only authorised and necessary connections can cross the CDE boundary.

### Trust Relationships and Security Dependencies

Systems outside the CDE may still affect its security through functions such as authentication, administrative access, logging, backup, or patch management. These relationships must be analysed to determine their potential impact on PCI DSS scope.

## Environment Architecture

MidTech's simulated payment environment is divided into four primary network zones:

### 1. Demilitarized Zone (DMZ)

The DMZ contains public-facing systems that receive traffic from external users and provide a controlled boundary between the internet and internal systems.

**Network:** `10.1.0.0/24`

### 2. Corporate Network

The Corporate Network contains internal business systems and services used by employees and administrators.

**Network:** `10.2.0.0/16`

Key services in this zone include:

- Active Directory (AD)
- Jump Host
- Corporate user systems

### 3. Management Network

The Management Network contains systems that support the operation, monitoring, backup, and maintenance of the environment.

Key services include:

- Splunk / SIEM
- Backup Server
- WSUS / Patch Management

### 4. Cardholder Data Environment (CDE)

MidTech identifies a dedicated network as its Cardholder Data Environment (CDE).

**Network:** `10.3.0.0/24`

The claimed CDE contains the following key components:

- Payment Application
- Card Database
- Hardware Security Module (HSM)
- Tokenization Service
- Log Collector

The CDE is separated from other network zones through network segmentation. This assessment examines whether the connections and dependencies between these zones are sufficiently controlled and whether the claimed CDE boundary can be supported by available evidence.

## Cardholder Data Flow

The cardholder data flow identifies how payment card data moves through MidTech's payment environment. Mapping this flow is a key step in identifying systems that store, process, or transmit Cardholder Data (CHD).

The simplified payment flow is:

Customer → Public-Facing Web Server → Payment Application → Card Database

During payment processing, the Payment Application may also interact with security services such as the Hardware Security Module (HSM) and Tokenization Service to protect cardholder data and reduce unnecessary exposure of the Primary Account Number (PAN).

The assessment uses the cardholder data flow to identify the core systems that form the claimed Cardholder Data Environment (CDE).

### Cardholder Data Flow Diagram

*Diagram to be added.*
