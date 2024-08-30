
___Why Zero Trust Matters to You___

The limitations of perimeter security and traditional data backup are driving the need for Zero Trust. A Zero Trust architecture assumes all users, devices, and applications are untrustworthy and can be compromised. Only users that have been authenticated using multi-factor methods get access to data and only to the data they need. Permissions and access are strictly limited, and users are unable to do anything malicious to stored data.

The Zero Trust model is defined by the National Institute of Standards (NIST), in the NIST SP 800-207 Zero Trust Architecture Specification. As NIST describes it, Zero Trust comprises “an evolving set of cybersecurity paradigms that move defenses from static, network-based perimeters to focus on users, assets, and resources.”

When it comes to protecting backup data, Zero Trust Data Security relies on six distinct capabilities

1) __Reduce Intrusion Risk__
The first line of defense in Zero Trust is preventing attackers from gaining access to data in the first place.

There are multiple methods to reduce unauthorized access:

- __Multi-factor authentication (MFA)__: MFA validates a combination of factors requested from a user. The most common factor is a user’s credentials. The second factor might be a Time-based One-Time Password (TOTP), biometric identifier, or key card. More factors can be used to further increase security. By combining something you know and something you have, MFA mitigates cyber-attacks and reduces the risk of unauthorized access. MFA should be considered a must have for access to backup systems and data.
- __Role-based access control (RBAC)__: RBAC restricts access based on an individual’s role within your organization or based on a service’s function. (Service accounts are created to allow third- party tools to have the necessary privileges to perform their functions.) Various user accounts and service accounts have different access privileges. Limiting access based upon role can greatly reduce the amount of data affected if a ransomware attack or other intrusion does occur.
- __Least privileged access__: Employees and services only get access to the resources necessary to perform their specific job duties—and nothing more. Even if a user is successfully authenticated, if they are not assigned to perform a specific task as defined by policy (based on factors such as authority, responsibility, and job competency), they are not granted access rights.

2) __Safeguard Backup Data From Compromise__

The next line of defense is to protect your backup data to the greatest extent possible—even if ransomware gains access. Again, there are multiple methods that should be employed:
• __Encryption__. Encrypting backup data ensures that if malware or a hacker gains access to your backup data, it cannot be read, reducing the risk that sensitive customer and employee data or valuable intellectual property (IP) will be breached. Ideally backup data should be encrypted both in-flight and at rest.
• __Immutability__. Because ransomware is able to encrypt already encrypted data and make it inaccessible, immutability is necessary to protect backup data from being encrypted by hackers or ransomware. Once data has been written, an immutable backup cannot be modified or deleted—either for a set period of time or forever. The technologies that underpin immutable data storage are often referred to by the acronym WORM (write-once-read-many).

By combining encryption and immutability, you can ensure that even if ransomware gains access to your data, it can neither render your backups unreadable nor exfiltrate data that compromises your company, your employees, or your customers.

3) __Detect Anomalous Activity For Faster Investigation__

Another important line of defense against ransomware for any organization is early detection. Delayed detection gives hackers more time to find and exploit vulnerabilities within your operations and can extend the time needed to recover fully.

Modern technologies that leverage machine learning models can help detect security threats through deep analysis of filesystems and access behavior. Backups may include rich metadata that can be securely analyzed to detect and generate alerts on anomalous activity using ML-based technologies. When unusual behavior is detected, IT teams should be alerted immediately to investigate, accelerating recovery if needed.

Some solutions use signature-based detection that compares patterns and sequences to known malware variants. However, by itself this is not always an effective approach since ransomware often mutates. In addition, signature-based detection only works when you are not the first victim—most ransomware attacks use a morphing and code obfuscation approach with a zero-day signature. Solutions that employ behavioral-based detection are often a better approach since they can catch these zero-day ransomware attacks.

4) __Discover and Manage Sensitive Data__

Another important line of defense is to identify and manage sensitive data ahead of time – before a breach occurs. At a minimum, this should include ensuring compliance with the laws and regulations of the region(s) in which you operate (such as GDPR and CCPA), industry-specific regulations (like HIPAA and PCI-DSS), and your own internal policies. If you suffer a ransomware attack, being out of compliance only adds to your troubles.

In practice, you need to make sure that you:

- Properly protect all new workloads
- Enforce data retention periods
- Have the ability to quickly identify any sensitive data that may have been exfiltrated

In a busy IT environment, automation is essential to ensure that these requirements are met and your policies are enforced—with compliance audits and regular reporting so that any problems can be quickly remediated

5) __Contain Incidents and Recover Quickly__

Full ransomware protection must also include the ability to contain any incidents that occur and return to normal operations quickly. Incident containment ensures that after an attack occurs you can fully contain it and avoid reinfection. Once ransomware enters your systems, it is necessary to quickly identify the scope of the infection, isolate all infected systems, and track signs of the infection backwards in time to the point of infiltration. Machine learning techniques can help you work your way back in time until you are certain that recovery can proceed with clean data.

Rapid recovery is essential to get your business back on its feet as quickly as possible with minimal disruption to business function. No organization is immune to cyber attacks but a long recovery time after an incident may create significant impacts to your business and its reputation. The ability to rapidly recover from an incident while minimizing data loss is critical to your bottom line. A comprehensive backup plan that is regularly tested is essential for minimizing losses.

Your backup and recovery solution should be designed for fast, reliable disaster recovery. Even in the event of a ransomware attack, it should be relatively straight forward to identify and restore to the most recent clean version of your data. Technologies that help automate the assessment of an attack’s impact and provide a clear view into what applications and files are infected or encrypted and where they reside, can enable your teams to quickly restore at a more granular level.


