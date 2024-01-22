
ClamAV® is an open source antivirus engine for detecting trojans, viruses, malware & other malicious threats.

One of its main uses is on mail servers as a server-side email virus scanner. ClamAV includes a number of utilities: a command-line scanner, automatic database updater and a scalable multi-threaded daemon, running on an anti-virus engine from a shared library. In this article you will learn in detail the installation and configuration of ClamAV.

The graphical frontend for the ClamAV antivirus called ClamTk.

Amavis (A Mail Virus Scanner) is a high-performance interface between a message transfer agent (MTA) such as Postfix and content filters. A content filter is a program that scans the headers and body of an email message. Most common tool of content filters are ClamAV virus scanner and SpamAssassin.

The ClamAV detection engine is multi faceted - heuristics, support for numerous archivers (Zip,Rar,OLE,etc,etc), tons of unpacking support (UPX, PeTite, NSPack, etc), and several different content inspection engines. These content inspection engines range from the simplistic (basic hashing signatures), to the extremely complex (ByteCode engine). In the middle are numerous content matching signature types that support everything you would expect from wildcards, character sets, Boolean logic, and negation. Support for PDF files, Javascript, and HTML files is also included in the engine, along with Mach-O binary support for all the shinny Apple devices out there. With all that support the ClamAV detection engine has everything necessary to detect today’s malware threats, exploits, adware, Trojans, spyware, keyloggers, and much more.

Sometimes detecting those threats requires some real heavy lifting. If that’s the case, the ByteCode engine allows a signature writer to do just about anything they can imagine. Need to implement a quick unpacker for that new piece of malware? Easy. Need to implement a new archiver to unpack something unique? Trivial. Have to do something complex with PDF files? No problem.

The other great thing about ClamAV is that the signature language is open, easy to use, and anyone can add new signatures to their ClamAV installs. If you’ve got something you need to do, and you need to do it now, cause your boss told you to, or the world is ending, it’s pretty darn simple to write your own signatures and add them to your setup. 

Heuristics are generally used in antivirus software alongside scanning solutions as a way to estimate where malicious code is on your computer. What may be referred to as a “heuristic virus” is the detection of possible malware, adware, trojans, or other threats. This preliminary warning may appear in a scan as “HEUR” and should be considered suspect code to further inspect.

Heuristic analysis can detect potential viruses without needing to specifically identify them. The process is agile and continually improves as it discovers threats. The longer it runs, the more efficient and effective it becomes. Unfortunately, heuristic analysis is labor-intensive and often results in false positives that must be manually reviewed.

## What Is Heuristic Analysis?

Heuristic analysis is based on several techniques. These techniques explore file source codes and match them with previously discovered threats. Depending on the proportion of the match, the system will find the probability of a threat and flag code that’s likely malicious.

Heuristic-based analysis uses a number of techniques to analyze behaviors and threat levels including:

    Dynamic scanning: Analyzes the behavior of a file in a simulated environment.
    File analysis: Analyzes the intent, destination, and purpose of a file.
    Multicriteria analysis (MCA): Analyzes the weight of the potential threat.

Heuristic virus scans use these analysis techniques for virus detection within code.



## Heuristic Virus Detection

Signature-based detection and sandboxing are used with heuristic virus detection for the most effective result.
Heuristic-based detection may determine code is a threat if the program:

    Persists in the memory after performing its task.
    Attempts to write to the disk.
    Modifies required operating system files.
    Mimics known malware.

## Heuristic Scanning

Adjusting the sensitivity level within heuristic scans determines the tolerance level of suspicious files. With an increased level of sensitivity, there is a greater level of protection, but also a higher risk of false positives.

Enable the heuristic scan and choose its sensitivity levels with the following steps:

    Open the settings in the main window of the program.
    Configure the scan properties in the scan section.
    Select the checkbox to enable the scan in the Heuristic section.
    To alter the sensitivity level, open settings and select one of the three levels.

Heuristic signatures exist for a variety of file types.  They are hardcoded into the clamav application.  A grep of the source code reveales the following:

`~/workspace/clamav-devel • grep -r "Heuristics\." ./libclamav`


## Understanding clamd, clamdscan and clamscan

When you run clamscan the libclamav engine and signatures are loaded at runtime. The other way to run the scanning engine is via clamd.

Clamd runs as a background process that has the engine and signatures in memory. A clamd client (clamdscan) then connects to the service in order to have the scanning performed. The clamd service accepts various commands in order to perform the scanning.

Configuration of the scanning is controlled via the clamd.conf configuration and cannot be specified at runtime. Whereas using clamscan it is possible to configure a large number of options at runtime from the command line.

Note that the clamd service is unauthenticated. Do not make it accessible from the Internet.

    clamd (or clamav-daemon) — Daemon that loads the virus database definitions into memory, and handles scanning of files when instructed to do so by clients such as clamdscan or clamonacc.
    freshclam daemon (or clamav-freshclam) — Daemon that periodically checks for virus database definition updates, downloads, installs them, and notifies clamd to refresh it’s in-memory virus database cache.
    clamdscan — Utility that allows you to scan the filesystem and ask clamd to scan a given set files.
    clamonacc daemon — Similar to clamdscan, but listens for file operations and asks clamd to scan files with activity. This daemon provides the On-Access Scanning functionality.

    
```
# Install the software
dnf install clamav clamd clamav-update -y
# packages  clamav clamav-lib clamav-data clamav-update

# Configure the SElinux for ClamAV. 
setsebool -P antivirus_can_scan_system 1
setsebool -P clamd_use_jit 1
getsebool -a | grep antivirus

# Configuring ClamAV

# remove Example string from the configuration file: 
sudo sed -i -e "s/^Example/#Example/" /etc/clamd.d/scan.conf
sed -i -e "s/^Example/#Example/" /etc/freshclam.conf

# Enable line in /etc/clamd.d/scan.conf
sed -i 's/#LocalSocket \/run/LocalSocket \/run/g' /etc/clamd.d/scan.conf

# To download latest Signature for ClamAV, virus definition database update

freshclam

# freshclam downloads 3 virus databases. CVD stands for ClamAV Virus Database.

    daily.cvd
    main.cvd
    bytecode.cvd

# To make freshclam automatically check for updates, you may run it with -d parameter
# This will check for updates every 2 hours.
freshclam -d

# There will be two systemd services installed by ClamAV:

clamd@amavisd.service: the Clam AntiVirus userspace daemon
clamav-freshclam.service: the ClamAV virus database updater

# Create ClamAV Systemd Service
vi /usr/lib/systemd/system/freshclam.service

[Unit]
Description = freshclam
After = network.target
[Service]
Type = forking
ExecStart = /usr/bin/freshclam -d -c 4
Restart = on-failure
PrivateTmp = true
RestartSec = 10sec
[Install]
WantedBy=multi-user.target

vi /usr/lib/systemd/system/clamd\@.service 
[Unit]
Description = clamd scanner daemon
After = syslog.target nss-lookup.target network.target
[Service]
Type = forking
ExecStart = /usr/sbin/clamd -c /etc/clamd.d/scan.conf
# Reload the database
ExecReload=/bin/kill -USR2 $MAINPID
Restart = on-failure
TimeoutStartSec=420
[Install]
WantedBy = multi-user.target


# Start and enable services
systemctl daemon-reload
systemctl start clamd@scan
systemctl start freshclam
systemctl enable clamd@scan
systemctl enable freshclam


```

```
# To scan the specified directory for viruses and immediately delete the infected files.
clamscan --infected --remove --recursive /var/www/

# scan all contents of the specified directory and move suspicious files to /tmp/clamscan and also record all information about the scanning process in the log file 
clamscan --infected --recursive --move=/tmp/clamscan -log=/var/log/clamscan.log /var/www 
```

Install Amavisd and other package needed.

```
yum -y install amavis
```

Commonly Viruses or spammer spread as attachments to email messages. Install the following packages for Amavis to extract and scan archive files in email messages such as .7z, .cab, .doc, .exe, .iso, .jar, and .rar files.

```
yum -y install arj bzip2 cpio file gzip nomarch spax unrar p7zip unzip zip lrzsz lzip lz4 lzop
```

Important:

If our server doesn’t use a fully-qualified domain name (FQDN) as the hostname, Amavis might fail to start. And the OS hostname might change, so it’s recommended to set a valid hostname directly in the Amavis configuration file.

```
vim /etc/amavisd/amavisd.conf
```

Find $mydomain and $myhostname. Like this.

```
$mydomain = 'example.com';   # a convenient default for other settings
```

Change to 

```
$mydomain = 'habibza.in';   # a convenient default for other settings
```

```
# $myhostname = 'host.example.com';  # must be a fully-qualified domain name
```

Change to :

```
$myhostname = 'mx.habibza.in'; # customize with your name
```

And then restart service amavis.

```
systemctl restart amavisd
```

Amavisd listens on 127.0.0.1:10024, we can see with `ss -lnpt`command.


Integrate Postfix SMTP Server With Amavis
Amavis works as an SMTP proxy. Email is fed to it through SMTP, processed, and fed back to the MTA through a new SMTP connection.

Run the following command, which tells Postfix to turn on content filtering by sending every incoming email message to Amavis, which listens on 127.0.0.1:10024.

```
postconf -e "content_filter = smtp-amavis:[127.0.0.1]:10024"
```

Also, run the following command. This will delay Postfix connection to content filter until the entire email message has been received, which can prevent content filters from wasting time and resources for slow SMTP clients.

```
postconf -e "smtpd_proxy_options = speed_adjust"
```
Then edit the master.cf file.

```
vim /etc/postfix/master.cf
```
Add the following lines at the end of the file. This instructs Postfix to use a special SMTP client component called smtp-amavis to deliver email messages to Amavis. Please allow at least one whitespace character (tab or spacebar) before each -o. In postfix configurations, a preceding whitespace character means that this line is continuation of the previous line.

```
smtp-amavis   unix   -   -   n   -   2   smtp
    -o syslog_name=postfix/amavis
    -o smtp_data_done_timeout=1200
    -o smtp_send_xforward_command=yes
    -o disable_dns_lookups=yes
    -o max_use=20
    -o smtp_tls_security_level=none
```

Then add the following lines at the end of the file. This tells Postfix to run an additional smtpd daemon listening on 127.0.0.1:10025 to receive email messages back from Amavis.

```
127.0.0.1:10025   inet   n    -     n     -     -    smtpd
    -o syslog_name=postfix/10025
    -o content_filter=
    -o mynetworks_style=host
    -o mynetworks=127.0.0.0/8
    -o local_recipient_maps=
    -o relay_recipient_maps=
    -o strict_rfc821_envelopes=yes
    -o smtp_tls_security_level=none
    -o smtpd_tls_security_level=none
    -o smtpd_restriction_classes=
    -o smtpd_delay_reject=no
    -o smtpd_client_restrictions=permit_mynetworks,reject
    -o smtpd_helo_restrictions=
    -o smtpd_sender_restrictions=
    -o smtpd_recipient_restrictions=permit_mynetworks,reject
    -o smtpd_end_of_data_restrictions=
    -o smtpd_error_sleep_time=0
    -o smtpd_soft_error_limit=1001
    -o smtpd_hard_error_limit=1000
    -o smtpd_client_connection_count_limit=0
    -o smtpd_client_connection_rate_limit=0
    -o receive_override_options=no_header_body_checks,no_unknown_recipient_checks,no_address_mappings
```

Install Amavisd and ClamAV


Save and close the file. Restart Postfix for the changes to take effect.

```
sudo systemctl restart postfix
```