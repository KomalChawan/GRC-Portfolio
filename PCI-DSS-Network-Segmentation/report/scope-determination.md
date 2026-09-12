# PCI DSS Scope Determination Document

## Document Information
| Field | Value |
|-------|-------|
| Organization | MidTech (Simulated Environment) |
| Assessment Date | 20 August 2026 |
| Assessor | Komal Chawan |
| Version | 1.0 |

---

## 1. Executive Summary

### Scope Statement

This assessment evaluates the PCI DSS scope of MidTech's payment environment and assesses whether the claimed Cardholder Data Environment (CDE) boundary at `10.3.0.0/24` effectively isolates systems that store, process, transmit, or could impact the security of cardholder data. The assessment identified cardholder data flowing through the Web Server tier to the Payment Application, as well as potential dependencies involving administrative access, identity services, logging, backup, and patch management. Based on the available architecture and data-flow information, the effectiveness of the claimed segmentation boundary cannot yet be confirmed without further technical evidence and validation.

### Key Findings

- [x] Cardholder data flows through the Web Server tier before reaching the Payment Application, making the Web Servers relevant to PCI DSS scope determination.
- [x] The claimed CDE segmentation cannot currently be demonstrated as effective without reviewing firewall rules and performing segmentation testing.
- [x] Administrative and shared-service dependencies, including the Jump Host, Active Directory, logging, backup, and patch management, require further validation to determine their impact on the final PCI DSS scope.

### Overall Risk Rating

High

---

## 2. Cardholder Data Environment Definition

### 2.1 Systems That STORE Cardholder Data

| System Name | Data Types Stored | Retention Period | Justification |
|-------------|------------------|------------------|---------------|
| Card Database Server (`10.3.0.20`) | PAN / cardholder data used for payment processing | To be validated | The data flow identifies the Card Database as the storage location for cardholder data. |
> **Assessment Note:** The CDE Log Collector (`10.3.0.50`) requires validation to determine whether application logs contain PAN or other cardholder data. If unmasked CHD is stored in logs, the logging environment must be considered during PCI DSS scope determination.

### 2.2 Systems That PROCESS Cardholder Data

| System Name | Processing Function | Data Elements | Justification |
|-------------|---------------------|---------------|---------------|
| Payment Application Server (`10.3.0.10`) | Processes payment transactions and handles payment card data received from the web tier | PAN / cardholder data | The documented data flow shows the Payment Application receiving cardholder data and interacting with downstream payment and security components. |
| HSM Appliance (`10.3.0.30`) | Performs cryptographic operations supporting protection of payment data | To be validated | The architecture identifies the HSM as a security component, but the exact data elements provided to the HSM require validation. |
| Tokenization Service (`10.3.0.40`) | Provides tokenization functionality for payment data | To be validated | The architecture includes a Tokenization Service, but the documented data flow does not confirm the exact cardholder data flow to this component. |

### 2.3 Systems That TRANSMIT Cardholder Data

| System Name | Transmission Path | Encryption | Justification |
|-------------|-------------------|------------|---------------|
| Web Servers (`10.1.0.10–10.1.0.12`) | Customer Browser → Web Server → Payment Application | HTTPS from customer browser; internal encryption requires validation | The documented data flow shows PAN being entered through the customer-facing web tier and transmitted to the Payment Application. |
| Payment Application Server (`10.3.0.10`) | Receives cardholder data from the Web Server and communicates with downstream payment components | Requires validation based on connection and cryptographic configuration | The documented data flow shows the Payment Application receiving PAN and transmitting payment data or related requests to downstream components. |

---

## 3. Connected Systems Assessment

### 3.1 Known or potential Direct Connections to CDE

| System | Connection Type | Business Purpose | In Scope? | Justification |
|--------|-----------------|------------------|-----------|---------------|
| Web Servers (`10.1.0.10–10.1.0.12`) | Direct application/data flow to Payment Application | Transmit payment requests and PAN from the public-facing web tier to the payment environment | Yes | The documented CHD data flow shows PAN passing through the Web Servers to the Payment Application. |
| SIEM / Splunk (`10.4.0.10`) | Logging connection to/from CDE requires validation | Centralised security monitoring and log analysis | To be determined | The Management Zone provides SIEM services, but the exact CDE logging path and security impact require validation. |
| Backup Server (`10.4.0.20`) | Backup connection to CDE requires validation | Backup and recovery of CDE systems and data | To be determined | Backup access may expose or affect CDE systems or stored cardholder data depending on backup scope and configuration. |
| WSUS / Patch Management (`10.4.0.30`) | Management connection to CDE requires validation | Patch deployment and maintenance | To be determined | A patch-management system may affect the security and operation of CDE systems if it manages them. |

### 3.2 Indirect Connections (Via Intermediary)

| System | Connection Path | Business Purpose | In Scope? | Justification |
|--------|-----------------|------------------|-----------|---------------|
| Corporate Workstations (`10.2.2.0/24`) | Corporate Workstation → Jump Host (`10.2.1.50`) → CDE systems | Administrative access to CDE systems | To be determined | The Jump Host provides a potential intermediary path between the Corporate Zone and CDE. Access controls, authentication, and firewall rules must be validated. |
| Active Directory Domain Controllers (`10.2.1.10–10.2.1.11`) | Active Directory → Authentication/authorisation services → CDE systems | Identity and authentication services | To be determined | If CDE systems depend on Active Directory for authentication, compromise of the identity service could affect access to CDE systems. The dependency requires validation. |

---

## 4. Security-Impacting Systems

### 4.1 Systems That Could Impact CDE Security

| System | Security Function | Impact if Compromised | In Scope? | Justification |
|--------|-------------------|----------------------|-----------|---------------|
| Active Directory Domain Controllers (`10.2.1.10–10.2.1.11`) | Provides identity and authentication services | Compromise could allow an attacker to manipulate accounts or authentication used to access CDE systems | Yes, if used for CDE authentication | A trusted identity service can directly impact the security of CDE access. |
| Jump Host (`10.2.1.50`) | Provides controlled administrative access to CDE systems | Compromise could provide an attacker with a path to administer or access CDE systems | Yes | The Jump Host provides an administrative path into the CDE and could directly affect its security. |
| SIEM / Splunk (`10.4.0.10`) | Centralised security monitoring and log analysis | Compromise or failure could reduce visibility of security events affecting the CDE | Security-impacting dependency | The SIEM may support monitoring of CDE security events; its final scope status depends on the confirmed monitoring architecture. |
| Backup Server (`10.4.0.20`) | Backup and recovery of systems and data | Compromise could expose backed-up cardholder data or allow unauthorised access to CDE backups | Security-impacting dependency | If the Backup Server stores CDE backups or has privileged access to CDE systems, it can impact the security of the CDE. |
| WSUS / Patch Management (`10.4.0.30`) | Patch deployment and maintenance | A compromised patch-management system could distribute malicious or unauthorised software to CDE systems | Security-impacting dependency | If WSUS manages CDE systems, it can directly impact their security and must be included in scope assessment. |

---

## 5. Trust Relationship Analysis

### 5.1 Authentication Dependencies

| CDE System | Authentication Source | Risk if Compromised | Mitigation |
|------------|----------------------|---------------------|------------|
| Payment Application Server (`10.3.0.10`) | Active Directory dependency to be validated | Compromise of a trusted identity source could potentially enable unauthorised access to the Payment Application | Validate authentication architecture; restrict privileged access; enforce MFA and least privilege |
| Card Database Server (`10.3.0.20`) | Authentication source to be validated | Compromise of an authentication dependency could potentially affect administrative access to stored cardholder data | Validate authentication architecture; restrict privileged access and monitor administrative access |
| Other CDE systems | Authentication source to be validated | External identity dependencies may affect the security of CDE administration | Document authentication dependencies and validate trust boundaries |

### 5.2 Shared Services

| Service | Used By CDE | Used By Non-CDE | Segmentation Effective? |
|---------|-------------|-----------------|------------------------|
| DNS | To be validated | To be validated | To be validated |
| NTP | To be validated | To be validated | To be validated |
| Active Directory | To be validated | Yes — Corporate Zone | To be validated |
| Backup | To be validated | To be validated | To be validated |
| Logging | Yes — CDE Log Collector | To be validated | To be validated |
| Patching | To be validated | To be validated | To be validated |

---

## 6. Segmentation Assessment

### 6.1 Segmentation Controls

| Control | Description | Effectiveness | Evidence |
|---------|-------------|---------------|----------|
| Firewall rules | The architecture uses network security controls to restrict traffic between the DMZ, Corporate Zone, CDE, and Management Zone. The firewall rules controlling access to the CDE must allow only authorised ports, protocols, and connections. | Not yet verified | Firewall rule base, allowed ports and protocols, and segmentation test results |
| VLAN / subnet separation | The environment is divided into separate network ranges for the DMZ (`10.1.0.0/24`), Corporate Zone (`10.2.0.0/16`), CDE (`10.3.0.0/24`), and Management Zone (`10.4.0.0/24`). | Implemented, effectiveness not verified | Network configuration, routing tables, VLAN configuration, and network diagrams |
| Access controls | Administrative access to CDE systems is intended to pass through the Jump Host, with identity and privileged access controls restricting who can administer CDE systems. | Partially verified | Jump Host configuration, privileged access lists, MFA configuration, and access logs |
| Monitoring | CDE security events are collected through the Log Collector and may be forwarded to the central SIEM for monitoring and analysis. | Implemented, coverage requires validation | Logging architecture, SIEM configuration, sample security logs, and monitoring records |

### 6.2 Segmentation Gaps

| Gap ID | Description | Risk | Remediation |
|--------|-------------|------|-------------|
| GAP-001 | The effectiveness of the claimed CDE segmentation cannot be confirmed without reviewing firewall rules and performing segmentation testing. | High | Review and document all rules crossing the CDE boundary, remove unnecessary access, and perform regular segmentation testing. |
| GAP-002 | The administrative access path from the Corporate Zone through the Jump Host to CDE systems requires validation to confirm that only authorised administrators and permitted connections can access the CDE. | High | Restrict administrative access using least privilege and MFA, validate permitted paths, block direct access, and retain administrative access logs. |
| GAP-003 | Management services, including logging, backup, and patch management, may create dependencies between the Management Zone and CDE that could affect segmentation or CDE security. | Medium | Document all management service connections, restrict access to required systems only, and assess each dependency for PCI DSS scope impact. |

---

## 7. QSA Challenge Preparation

### 7.1 Anticipated Questions

| Area | Likely Question | Your Response | Evidence |
|------|-----------------|---------------|----------|
| Shared Services | Which shared services outside the CDE are used by CDE systems, and how can they affect CDE security? | Active Directory, backup, logging, and patch management dependencies have been identified for assessment. Each dependency requires validation to determine its connection path and security impact. | Service dependency documentation, network diagrams, firewall rules, and system configurations |
| Admin Access | How is administrative access from the Corporate Zone to the CDE restricted and controlled? | Administrative access is expected to be mediated through the Jump Host. The permitted access paths, authentication controls, MFA, privileged access restrictions, and logging require validation. | Jump Host configuration, firewall rules, MFA configuration, privileged access lists, and access logs |
| Logging | Does the CDE transmit PAN or other cardholder data to the Log Collector or SIEM? | The current architecture identifies logging components, but the contents of payment and application logs require validation. The assessment must confirm whether PAN is excluded or appropriately protected. | Application logging configuration, sample logs, PAN masking configuration, and SIEM/log collector configuration |
| Backups | Do backups contain cardholder data, and can the Backup Server access CDE systems or stored CHD? | Backup scope and contents have not yet been confirmed. The assessment requires validation of whether CDE systems or cardholder data are included in backups. | Backup configuration, backup inventory, sample backup records, encryption settings, and access control configuration |

---

---

## 8. Recommendations

### 8.1 Immediate Actions (0–30 days)

1. Review and document all firewall rules, ports, protocols, and permitted connections crossing the CDE boundary.
2. Validate the administrative access path through the Jump Host, including authorised users, MFA, privileged access, and connections permitted to CDE systems.
3. Identify and document all CDE dependencies on logging, backup, patch management, and identity services.

### 8.2 Short-Term Actions (30–90 days)

1. Perform and document segmentation testing to verify that non-authorised systems cannot access the CDE.
2. Review logging configurations to confirm that PAN and other sensitive cardholder data are not unnecessarily stored in application or centralised logs.
3. Review backup configurations to identify whether backups contain cardholder data and ensure appropriate encryption and access controls.

### 8.3 Long-Term Actions (90+ days)

1. Establish a periodic segmentation testing and scope review process.
2. Maintain an up-to-date inventory of CDE systems, connected systems, security-impacting systems, and trust relationships.
3. Review architecture changes, new integrations, and shared service dependencies before implementation to assess their potential impact on PCI DSS scope.

---

---

## 9. Residual Risk Statement

After implementing the recommended controls, some residual risk may remain due to necessary connections and dependencies between the CDE and supporting systems.

| Risk | Likelihood | Impact | Acceptance Required? |
|------|------------|--------|---------------------|
| Compromise of a trusted shared service could affect the security of connected CDE systems | Low | High | Yes |
| Authorised administrative access through the Jump Host could be abused if privileged credentials are compromised | Low | High | Yes |
| Backup or logging systems supporting the CDE may remain security-impacting dependencies | Low | Medium | Yes |

---

## Appendices

### A. Network Diagram



### B. Data Flow Diagram


### C. Evidence Index
| Evidence ID | Description | Location |
|-------------|-------------|----------|
| EV-001 | Current-state Network Architecture Diagram showing DMZ, Corporate Zone, CDE, Management Zone, and claimed segmentation boundary | Appendix A |
| EV-002 | Cardholder Data Flow Diagram showing the flow of PAN from Customer Browser to Payment Application and downstream systems | Appendix B |
| EV-003 | Firewall rule base and permitted ports/protocols crossing the CDE boundary | To be collected / validated |
| EV-004 | Segmentation testing results demonstrating that unauthorised systems cannot access the CDE | To be collected / validated |
| EV-005 | Jump Host configuration and administrative access records for CDE systems | To be collected / validated |
| EV-006 | Active Directory authentication dependency and privileged access configuration for CDE systems | To be collected / validated |
| EV-007 | Application and centralised logging configuration, including validation of PAN handling in logs | To be collected / validated |
| EV-008 | Backup configuration and inventory showing whether backups contain cardholder data | To be collected / validated |
| EV-009 | Patch management configuration showing whether CDE systems are managed by WSUS | To be collected / validated |

---


