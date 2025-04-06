# SOC & Honeynet Implementation in Azure (Live Traffic)

## Objective
<img width="1259" alt="Screenshot 2025-04-03 at 5 38 03 PM" src="https://github.com/user-attachments/assets/6fa75ea2-97b8-4801-80b1-6bc8c7d466a2" />

In this lab, I deployed a honeynet using intentionally vulnerable virtual machines (VMs), monitor and log security events on those VMs, establish a Security Operations Center (SOC) with Microsoft Sentinel, and then harden the environment against potential attacks. 

The honeynet primarily consisted of a Windows 10 VM and a Linux VM. Security logs from various sources were collected into a Log Analytics workspace, which Microsoft Sentinel leveraged to generate attack maps, trigger alerts, and create security incidents. The vulnerable environment was run for 24 hours to capture baseline security metrics. Afterwards, security controls—guided by NIST 800-53 recommendations—were applied to harden the VMs. The environment was then monitored for another 24 hours to measure the impact of the implemented controls.

### Honeynet Architecture
The following components were deployed in the honeynet architecture:

- Azure Storage Account
- SQL Server Management Studio (SSMS)
- Virtual Machines (2 Windows, 1 Linux)
- Azure Active Directory
- Network Security Group (NSG)
- Microsoft SQL Server
- Azure Key Vault
- Virtual Network (VNet)

In addition, the SOC incorporated the following regulations, tools, and components:

- PCI DSS 3.2.1
- NIST SP 800-53 Revision 4
- Kusto Query Language (KQL)
- Log Analytics Workspace
- Microsoft Defender for Cloud (MDC)
- Windows Event Viewer
- Microsoft Sentinel (SIEM)
- PowerShell

## Pre-hardening Attack Maps
To gather metrics for the insecure environment, all resources were initially deployed with full exposure to the public internet. The Virtual Machines had their Network Security Groups (NSGs) configured to allow all inbound traffic, and their built-in firewalls were disabled. Additionally, all other resources were deployed with publicly accessible endpoints.

<img width="1245" alt="Screenshot 2025-04-03 at 1 56 52 PM" src="https://github.com/user-attachments/assets/712c1dfe-7611-44fd-bd9e-e77fd2ade416" />

> This attack map highlights the risks associated with leaving the Network Security Group (NSG) open, as it permitted unrestricted malicious traffic. The visualization emphasizes the critical need for enforcing proper security controls—such as tightening NSG rules—to block unauthorized access and reduce exposure to potential threats.

<img width="1112" alt="Screenshot 2025-04-03 at 1 56 03 PM" src="https://github.com/user-attachments/assets/b125242d-1d8b-4ab9-bc70-9443420646a5" />

> This attack map reveals a high volume of syslog authentication failures on the deployed Linux server, indicating repeated unauthorized access attempts from external sources. It underscores the importance of securing Linux systems with robust authentication methods and continuously monitoring system logs for signs of intrusion.

<img width="1141" alt="Screenshot 2025-04-03 at 1 57 10 PM" src="https://github.com/user-attachments/assets/319323de-a493-459f-8de0-16f63649d021" />

> This attack map displays a significant number of RDP and SMB authentication failures, highlighting persistent attempts by threat actors to exploit these services. The visualization reinforces the importance of securing remote access and file-sharing protocols to defend against unauthorized access and mitigate cyber threats.

## Post-hardening Attack Maps

To gather metrics for the secured environment, Network Security Groups were hardened by blocking all traffic except from my workstation, and built-in firewalls were activated. Access to Azure Key Vault and the Storage Container was secured by disabling public endpoints and replacing them with private endpoints.

>All map queries returned no results, as there were no instances of malicious activity during the 24-hour period following the hardening process.

## Metrics Before Hardening / Security Controls

The table below presents the metrics collected from the insecure environment after 24 hours:

Start Time 2025-26-03 20:02:16 
Stop Time 2025-27-03 20:02:16

| Metric  | Count |
| ------------- | ------------- |
| SecurityEvents (Windows VM)  | 23,569  |
| Syslog (Linux VM)  | 14,659  |
| SecurityAlert (Microsoft Defender for Cloud)  | 0  |
| SecurityIncident (Sentinel Incidents) | 241  |
| NSG Inbound Malicious Flows Allowed  | 1890  |

## Metrics After Hardening / Security Controls

The table below presents the metrics recorded after applying security controls to the environment and monitoring it for an additional 24 hours:

Start Time 2025-28-03 20:25:32 
Stop Time 2025-29-03 20:25:32

| Metric  | Count |
| ------------- | ------------- |
| SecurityEvents (Windows VM)  | 871  |
| Syslog (Linux VM)  | 69  |
| SecurityAlert (Microsoft Defender for Cloud)  | 0  |
| SecurityIncident (Sentinel Incidents) | 0  |
| NSG Inbound Malicious Flows Allowed  | 0  |

### The Effect of Security Controls

| Metric  | Change post-hardening |
| ------------- | ------------- |
| SecurityEvents (Windows VM)  | 96.3% decrease  |
| Syslog (Linux VM)  | 99.53% decrease  |
| SecurityAlert (Microsoft Defender for Cloud)  | 100% decrease |
| SecurityIncident (Sentinel Incidents) | 100% decrease |
| NSG Inbound Malicious Flows Allowed  | 100% decrease |

## Conclusion

The comparison of metrics before and after implementing the security controls showed a significant decrease in security events and incidents, underscoring the effectiveness of the applied measures. It's worth noting that if the network's resources had been more actively used by regular users, a higher volume of security events and alerts might have been generated within the 24-hour period following the implementation of these controls.
