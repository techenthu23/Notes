
- [X.509 certificate](#x509-certificate)
  - [What Does an X.509 Certificate Contain?](#what-does-an-x509-certificate-contain)
- [Certificate Format](#certificate-format)
  - [PEM](#pem)
- [Tools](#tools)
  - [openssl tool](#openssl-tool)
- [Best Practices](#best-practices)

# X.509 certificate

X.509 is one of the standards for defining public-key certificates. It’s the most commonly used one on the internet. The certificate contains information about the public key and the entity associated with the public key. The entity can be anything, but most commonly, they’re usually associated with a person, an internet service, or a computer.

The secure socket layer (SSL) and transport layer security (TLS) are two common protocols that utilize the X.509 certificate to establish an end-to-end encrypted connection between two hosts to secure a channel before other communication takes place.

In the TLS and SSL cryptographic protocols, a public key certificate is an electronic certificate that a website presents to the end-user. Through the certificate, a website can prove its legitimacy to its visitors. Visitors can then confidently interact with the website.

## What Does an X.509 Certificate Contain?

The below certificate snippet is for google.com.

```
Certificate:
    Data:        
        Serial Number:
            24:4e:52:d9:6b:55:1f:96:0a:00:00:00:00:f2:ba:f4
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = US, O = Google Trust Services LLC, CN = GTS CA 1C3
        Validity
            Not Before: Jul 12 01:35:31 2021 GMT
            Not After : Oct  4 01:35:30 2021 GMT
        Subject: CN = *.google.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature
            X509v3 Extended Key Usage:
                TLS Web Server Authentication
            X509v3 Subject Alternative Name:
                DNS:*.google.com, DNS:*.appengine.google.com, DNS:*.bdn.dev, 
                ...(truncated)
    Signature Algorithm: sha256WithRSAEncryption
         c1:0b:9e:6b:58:ea:5f:31:c8:25:1a:49:b6:fc:dd:a6:46:73:
         ...(truncated)
```

Firstly, every certificate contains a __Serial Number__. It’s a value given by the issuer when it signs the certificate.

The issuer of the certificate is defined under the field Issuer. For this particular certificate, the issuer is under the Google Trust Services LLC organization that’s residing in the US. Additionally, the issuer has a common name of GTS CA 1C3.

Next, the Validity field defines the period during which a certificate is effective. Particularly, a certificate is only valid during the period defined by the Not Before and Not After.

On the Subject field, we can see that this certificate has a common name of *.google.com. This is a wildcard common name that allows all the subdomains of google.com to identify themselves using the same certificate. Furthermore, the Subject Public Key Info field specifies the public key for this certificate. Beneath the same field, the certificate also defines the algorithm type of the public key as well as the necessary parameters.

In the X509v3 extensions field, we can find several extended properties that are on version 3 of the X.509 certificate standard. For example, the X509v3 Subject Alternative Name field defines other domains that are authenticating using the same certificates. In other words, this certificate would also be valid for the *.cloud.google.com,*.appengine.google.com, and so on.

Finally, we can see a Signature Algorithm field followed by a block of hexadecimal. This field specifies the algorithm that is used when the signature is made. In this case, it’s the sha256WithRSAEncryption. Additionally, the block of hexadecimal is the signature signed by the issuer. The signature serves to make sure the information on the certificate has not been trampled after it has been verified by the certificate authority.

In every X.509 certificate, there’s a common name (CN) attribute. This attribute tells the certificate receiver the name of the entity that owns the public key in the certificate. Besides that, there’s an X.509 extension, subject alternative names (SAN), that stores other identifiers. In the context of HTTPS, the CN, and SAN will contain domain names or, in some cases, the IP addresses of the server.

In public-key cryptography, also known as asymmetric cryptography, the encryption mechanism relies upon two related keys, a public key and a private key. The public key is used to encrypt the message, while only the owner of the private key can decrypt the message.

# Certificate Format

- X.509 is a standard defining the format of public-key certificates.
- DER is the most popular encoding format to store data, like X.509 certificates, and PKCS8 private keys in files. It’s a binary encoding, and the resulting content can’t be viewed with a text editor.
- PKCS8 is a standard syntax for storing private key information. The private key can be optionally encrypted using a symmetric algorithm. Not only can RSA private keys be handled by this standard, but also other algorithms. The PKCS8 private keys are typically exchanged through the PEM encoding format.
- PEM is a base-64 encoding mechanism of a DER certificate. PEM can also encode other kinds of data, such as public/private keys and certificate requests.

## PEM

The certificates in PEM format are base64 encoded

# Tools

## openssl tool

The openssl tool is a cryptography library that implements the SSL/TLS network protocols. It contains different subcommands for any SSL/TLS communications needs.

For instance, the s_client subcommand is an implementation of an SSL/TLS client. Besides that, the x509 subcommand offers a variety of functionality for working with X.509 certificates.

```
# Fetching the X.509 Public Key Certificate File 
openssl s_client -connect google.com:443 -showcerts </dev/null | openssl x509 -outform pem > googlecert.pem

# Extracting specific fields from the Certificate 
openssl x509 -in googlecert.pem -noout -subject
openssl x509 -in googlecert.pem -noout -issuer
openssl x509 -in googlecert.pem -noout -ext subjectAltName
openssl x509 -in googlecert.pem -noout -ext keyUsage
openssl x509 -in googlecert.pem -noout -dates
openssl x509 -in googlecert.pem -noout -startdate
openssl x509 -in googlecert.pem -noout -enddate
openssl x509 -in googlecert.pem -noout -fingerprint -serial
openssl x509 -in googlecert.pem -noout -pubkey

# Formating the Output
openssl x509 -in googlecert.pem -noout -issuer -nameopt lname -nameopt sep_multiline
openssl x509 -noout -subject -in google-cert.pem -nameopt multiline 

# Checking if a Certificate Is About to Expire in n unit of seconds
openssl x509 -in certificate-filename -noout -checkend n

# Obtaining an X.509 Certificate
penssl s_client -showcerts -connect  google.com:443 </dev/null |openssl x509 -outform PEM > google-cert.pem
```

# Best Practices

When generating a certificate, it's essential to consider both the __Common Name (CN)__ and the __Subject Alternative Names (SAN)__. Let's break down the best practices for each:

1. __Common Name (CN)__:
   - The CN is an attribute in an X.509 certificate that identifies the entity (such as a server or client) associated with the public key.
   - Traditionally, the CN was used to specify the primary domain name for which the certificate was issued.
   - However, using the CN alone is __deprecated__. Certification Authorities (CAs) now encourage using the __dNSName__ (domain name) in the SAN extension instead.
   - For HTTPS certificates, servers should primarily rely on the SAN for identification and use the CN for backward compatibility¹².

2. __Subject Alternative Names (SAN)__:
   - The SAN extension allows you to include additional identifiers (such as domain names, IP addresses, or email addresses) in the certificate.
   - Best practices for SAN include:
     - __Always include the primary domain name__ (e.g., `www.example.com`) as a SAN.
     - Add any additional domain names (subdomains, aliases) that the certificate should cover.
     - If you have multiple domains, list them all in the SAN extension.
     - Avoid relying solely on the CN; use the SAN for identity validation.
     - SANs are checked first during validation, and if found, the CN is not checked¹.
     - Note that some older implementations (like Internet Explorer) may ignore the CN if the SAN is present¹³.

In summary, prioritize the SAN extension over the CN for specifying domain names in certificates. This approach ensures better compatibility and security across modern browsers and applications. Remember to include all relevant domain names in the SAN to cover all use cases⁴⁵. 🌟

[RFC 5280](https://www.rfc-editor.org/rfc/rfc5280#section-4.1.2.6), section 4.1.2.6 says "The subject name MAY be carried in the subject field and/or the subjectAltName extension". This means that the domain name must be checked against both SubjectAltName extension and Subject property (namely its common name parameter) of the certificate. These two places complement each other, not duplicate each other. And SubjectAltName is a proper place to put additional names, such as <www.domain.example> or www2.domain.example

Update: as per [RFC 6125](https://www.rfc-editor.org/rfc/rfc6125#section-6.4.4), published in 2011, the validator must check SAN first, and if SAN exists, then CN should not be checked. Note that RFC 6125 is relatively recent and there still exist certificates and CAs that issue certificates, which include the "main" domain name in CN and alternative domain names in SAN. In other words, by excluding CN from validation if SAN is present, you can deny some otherwise valid certificate.

Source: Conversation with Bing, 17/5/2024
(1) ssl - How do Common Names (CN) and Subject Alternative Names (SAN) work .... <https://stackoverflow.com/questions/5935369/how-do-common-names-cn-and-subject-alternative-names-san-work-together>.
(2) Is the Common Name mandatory for digital certificates?. <https://security.stackexchange.com/questions/55414/is-the-common-name-mandatory-for-digital-certificates>.
(3) security - What strings are allowed in the "common name" attribute in .... <https://stackoverflow.com/questions/5136198/what-strings-are-allowed-in-the-common-name-attribute-in-an-x-509-certificate>.
(4) GlobalProtect Certificate Best Practices - Palo Alto Networks. <https://docs.paloaltonetworks.com/globalprotect/9-1/globalprotect-admin/get-started/enable-ssl-between-globalprotect-components/globalprotect-certificate-best-practices>.
(5) Common Name and Subject Alternative Names in a X.509 Certificate - Baeldung. <https://www.baeldung.com/linux/x-509-certificate-common-name-subject-alternative-names>.
(6) en.wikipedia.org. <https://en.wikipedia.org/wiki/Subject_Alternative_Name>.
