# Building a SOC + Honeynet in Azure (Live Traffic)
![Screenshot 2023-07-27 171837](https://github.com/ChanooKim/Azure-SOC/assets/87055300/cdf9e845-8909-49c1-9bc1-7e2a9b03c6d0)


## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://github.com/ChanooKim/Azure-SOC/assets/87055300/ef283c7c-1b96-48e8-a4aa-0c89dc1c5ce5)<br>
![Linux Syslog Auth Failures](https://github.com/ChanooKim/Azure-SOC/assets/87055300/efbec427-bfa7-4401-8b0c-0d9c8ee065bf)<br>
![Windows RDP/SMB Auth Failures](https://github.com/ChanooKim/Azure-SOC/assets/87055300/081ebf58-02fa-4147-89b0-868807e9717f)<br>
![MSSQL Auth Failures](https://github.com/ChanooKim/Azure-SOC/assets/87055300/13d952a5-a74c-4a34-a6ea-e36296bf4a8f)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-07-02 17:04:29
Stop Time 2023-07-03 17:04:29

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 14418
| Syslog                   | 11571
| SecurityAlert            | 10
| SecurityIncident         | 422
| AzureNetworkAnalytics_CL | 907

## Incident Response

Incident Response was carried out using NIST 800-61 Incident Management Lifecycle

![image](https://github.com/ChanooKim/Azure-SOC/assets/87055300/28f46727-7def-4c69-9a5f-47057af06fdf)

**Step 1: Preparation**
- Ingest all logs into Log Analytics Workspace and Sentinel
- Configure alert rules

**Step 2: Detection & Analysis**
1. Set Severity, Status, Owner
2. View Full Details (New Experience)
3. Observe the Activity Log (for history of incident)
4. Observe Entities and Incident Timelines (are they doing anything else?)
5. “Investigate” the incident and continue trying to determine the scope
6. Inspect the entities and see if there are any related events
7. Determine legitimacy of the incident (True Positive, False Positive, etc.)
8. If True Positive, continue, if False positive, close it out

**Step 3: Containment, Eradication, and Recovery**
- Use incident response playbook for each incident

**Step 4: Post-incident Activity**
- Document findings/info and close out the incident in Sentinel

## Hardening Cloud Environment with Microsoft Defender for Cloud 

![Screenshot 2023-07-28 221422](https://github.com/ChanooKim/Azure-SOC/assets/87055300/9e79c9c2-5090-454f-8475-d4b0e0640a78)

Following the incident response process, to prevent further incidents the cloud environment was hardened by following NIST 800-53 and PCI DSS 4 controls. 


## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-07-06 15:37
Stop Time	2023-07-07 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 705
| Syslog                   | 24
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

The following table shows the percentage change after implementing the security controls: 

| Metric                   | % Change after securing environtment
| ------------------------ | -----
| SecurityEvent            | -95.11%
| Syslog                   | -99.79%
| SecurityAlert            | -100.00%
| SecurityIncident         | -100.00%
| AzureNetworkAnalytics_CL | -100.00%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
