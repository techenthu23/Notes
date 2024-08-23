 OpenSCAP allows to automatically remediate systems that have been found in a non-compliant state. For system remediation, an XCCDF (The Extensible Configuration Checklist Description Format) file with instructions is required. The scap-security-guide package constains certain remediation instructions.
System remediation consists of the following steps:

    - OpenSCAP performs a regular XCCDF evaluation.
    - An assessment of the results is performed by evaluating the OVAL definitions. Each rule that has failed is marked as a candidate for remediation.
    - OpenSCAP searches for an appropriate fix element, resolves it, prepares the environment, and executes the fix script.
    - Any output of the fix script is captured by OpenSCAP and stored within the rule-result element. The return value of the fix script is stored as well.
    - Whenever OpenSCAP executes a fix script, it immediatelly evaluates the OVAL definition again (to verify that the fix script has been applied correctly). During this second run, if the OVAL evaluation returns success, the result of the rule is fixed, otherwise it is an error.
    - Detailed results of the remediation are stored in an output XCCDF file. It contains two TestResult elements. The first TestResult element represents the scan prior to the remediation. The second TestResult is derived from the first one and contains remediation results. 

There are three modes of operation of OpenSCAP with regard to remediation: online, offline, and review. 

XCCDF is a specification language for writing security checklists, benchmarks, and related kinds of documents. An XCCDF document represents a structured collection of security configuration rules for some set of target systems. The specification is designed to support information interchange, document generation, organizational and situational tailoring, automated compliance testing, and compliance scoring. The specification also defines a data model and format for storing results of benchmark compliance testing. The intent of XCCDF is to provide a uniform foundation for expression of security checklists, benchmarks, and other configuration guidance, and thereby foster more widespread application of good security practices.

XCCDF documents are expressed in XML, and may be validated with an XML Schema-validating parser.

Development of the XCCDF specification is being led by NIST, with contributions from other agencies and organizations. The XCCDF specification document and related files for various revisions can be downloaded below. A mailing list for XCCDF developers is available, please subscribe to participate in discussions. A publicly available archive of the XCCDF mailing list is also available.



```sh
dnf install openscap-scanner
dnf install scap-security-guide

ls -1 /usr/share/xml/scap/ssg/content/ssg-*-ds.xml

oscap info /usr/share/xml/scap/ssg/content/ssg-fedora-ds.xml


oscap xccdf eval \
 --profile xccdf_org.ssgproject.content_profile_cusp_fedora \
 --results-arf /root/oscap_fedorra_arf.xml \
 --report /root/oscap_fedorra_report.html \
 /usr/share/xml/scap/ssg/content/ssg-fedora-ds.xml

oscap xccdf eval \
 --remediate \
 --profile xccdf_org.ssgproject.content_profile_cusp_fedora \
 --report /root/oscap_fedorra_report.html \
 /usr/share/xml/scap/ssg/content/ssg-fedora-ds.xml


```