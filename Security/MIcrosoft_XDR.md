
[img1]: ../Gallery/m365-defender-eval-threat-chain.png

# Microsoft XDR Defined

Microsoft Defender XDR is an **eXtended Detection and Response** (XDR) solution that automatically collects, correlates, and analyzes signal, threat, and alert data from across your Microsoft 365 environment, including endpoint, email, applications, and identities. It leverages artificial intelligence (AI) and automation to automatically stop attacks, and remediate affected assets into a safe state.

Microsoft Defender XDR is a Cloud-based, unified, pre- and post-breach enterprise defense suite. It coordinates prevention, detection, investigation, and response across endpoints, identities, apps, email, collaborative applications, and all of their data to provide integrated protection against sophisticated attacks.

Here's a list of the different Microsoft Defender XDR products and solutions that Microsoft Defender XDR coordinates with:

- Microsoft Defender for Endpoint
- Microsoft Defender for Office 365
- Microsoft Defender for Identity
- Microsoft Defender for Cloud Apps
- Microsoft Defender Vulnerability Management
- Microsoft Entra ID Protection
- Microsoft Data Loss Prevention
- App Governance

Often abbreviated as XDR, Extended Detection and Response, is defined by Gartner as an SaaS-based, vendor-specific threat detection and incident response tool that integrates multiple security products into a cohesive security operations system by unifying all licensed components.

Microsoft’s XDR platform provides a solution for modern security challenges such as the integration of multi-cloud hybrid security environments. Microsoft XDR broadens the scope of security while eliminating silos by integrating protection across an organization’s endpoints, servers, cloud applications, emails, and more. From there, Microsoft XDR solutions combine threat prevention, detection, investigation, and threat response, providing visibility, analytics, and automated responses to mitigate the risk of cyber threats.

## What is the difference between XDR and managed detection and response (MDR)?

Both MDR and XDR provide organizations with tools and personnel for threat hunting and incident management. Both solutions act to augment an organization’s existing cybersecurity capabilities and respond to threats faster.  

**Managed XDR** or **MXDR** extends the MDR framework into the endpoint; effectively providing visibility into the entire security environment and all its attack surfaces. MXDR includes the ability to correlate telemetry data across the network to deploy a cohesive real-time response to identified threats across the security network.

Today, MDR solutions often use XDR systems to meet an enterprise’s security needs, but not always. Learn what it means to be MXDR certified.  

## What is the difference between XDR and endpoint detection and response (EDR)?

Today, XDR represents an evolution of Endpoint Detection Response (EDR) that provides security teams with more information from the security environment, beyond just the endpoint.  

EDR is focused on providing in-depth visibility and threat prevention for a particular device to protect each endpoint. XDR takes a wider view, integrating security across an organization’s endpoints, servers, cloud applications, emails, and more. While EDR is a necessary and effective solution to protect an organization’s endpoints, XDR is designed to provide integrated visibility and threat management within a single solution to consolidate the security environment and remove silos within the network.

## How does XDR work with SIEM?

Microsoft XDR complements existing enterprise security information and event management (SIEM) systems like Microsoft Sentinel. Primarily, SIEM technology aggregates large quantities of shallow data and identifies security threats but cannot respond to or remediate threats. SIEMs typically require manual responses to anomalous behaviors. XDR takes advantage of the data SIEMs make available and offers automated response capabilities to protect against threats.

# Illustration

In this illustration an attack is underway. Phishing email arrives at the Inbox of an employee in your organization, who unknowingly opens the email attachment. This installs malware, which leads to a chain of events that could end with the theft of sensitive data. But in this case, Defender for Office 365 is in operation.

The various attack attempts

In the illustration:

![M365-defender-eval-threat-chain][img1]

Exchange Online Protection, part of Microsoft Defender for Office 365, can detect the phishing email and use mail flow rules (also known as transport rules) to make certain it never arrives in the Inbox.

Defender for Office 365 uses Safe Attachments to test the attachment and determine that it's harmful, so the mail that arrives either isn't actionable by the user, or policies prevent the mail from arriving at all.

Defender for Endpoint manages devices that connect to the corporate network and detect device and network vulnerabilities that might otherwise be exploited.

Defender for Identity takes note of sudden account changes like privilege escalation, or high-risk lateral movement. It also reports on easily exploited identity issues like unconstrained Kerberos delegation, for correction by the security team.

Microsoft Defender for Cloud Apps notices anomalous behavior like impossible-travel, credential access, and unusual download, file share, or mail forwarding activity and reports these to the security team.


# Reference

- [Microsoft Defender XDR Documentation](https://learn.microsoft.com/en-us/microsoft-365/security/defender/?view=o365-worldwide)
