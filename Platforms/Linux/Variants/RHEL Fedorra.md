
CentOS Linux, when it existed, matched a specific RHEL version.

The name Fedora was invented to distinguish the community edition, with frequent releases and short-term active cycles, from the enterprise edition, with less frequent releases and long-term activity, of Red Hat's distribution of an operating system built on GNU tooling and a Linux kernel.

Wikipedia article says CentOS started with RHEL 3, but the first-party citations I can find only have dates going back to CentOS 4. Everyone scrambling in 2024 knows that it ended with CentOS 8 being cut short, so the last version is CentOS Linux 7. "CentOS Stream" will continue but sit between Fedora and RHEL, instead of being identical to RHEL.

Wikipedia citations and Red Hat published information seem to differ on which version of Fedora Linux a version of Red Hat Enterprise Linux essentially forked from. Fedora's current documentation match's Red Hat's, except that Fedora's includes its version used for RHEL 9, but Red Hat's does not.

    Fedora Core 3 to RHEL 4
    Fedora Core 6 to RHEL 5
    Fedora 10 (Wikipedia), Fedora 12, 13 to RHEL 6
    Fedora 18, 19, 20 (Wikipedia), Fedora 19, 20 to RHEL 7
    Fedora 28 to RHEL 8
    Fedora 34 to RHEL 9
    Fedora 40? in 2024 for RHEL 10 due in 2025

# Release Cadence and Active Length

Fedora

    release: 6 months
    active: 1 year

That means there are only ever two active versions.

Technically, it is more like 7 months and maybe 13 or 14 months because the Fedora Project allows for an upgrade period around releases.

For example, this spring Fedora 40 is being released. Version 38 will not longer be maintained in general but issues found when upgrading in-place, directly from 38 to 40 will be patched in 38 for a month or so depending severity and impact. If a users is still on 37 and finds an issue, a patch will not be sent out. However, generally it is considered safe to have a user that fell behind be able to go from 37 to 38, then 38 to 39, and finally 39 to 40. It is not recommend that they try to jump from 37 to 40.
CentOS Stream

    release: 3 years
    active: ~5 years

Not finding a lifecycle commitment from first-party information on CentOS Stream.

The homepage and blog announcement for CentOS Stream 9 seem to be saying that, though it will fork Fedora Linux earlier, it will basically match releases with RHEL. It goes on to indicate Stream will only have an active period for about half the time of RHEL. This is identified by the phrasing "EOL: End of RHEL9 'full support' phase" on the CentOS page and "Updates for the distribution continue through the full RHEL support phase" on a Red Hat general FAQ about the creation of CentOS Stream. That phase being the first 5 years of the phases in RHEL.
Red Hat Enterprise Linux (and previously CentOS Linux)

    release cycle: 3 years
    active: 10 years

Release cycles have varied between about 3 to 5 years. Around RHEL 5, 6, and 7, it was that one was on its way out, one was stable and only given security patches, and one was new and highly active. With the current stepping, 7 will be the last of that as 10 comes out next year, but going forward, at least for now, it would mean that 8, 9, 10, 11 would be available at the same time with about a year overlap between 8 and 11 before 12 comes out. Time will tell if this stepping holds. The stability of features and the current ease of working in software outside of the operating system's stack makes it seem promising.
References

    https://access.redhat.com/articles/3078
    https://docs.fedoraproject.org/en-US/quick-docs/fedora-and-red-hat-enterprise-linux/index.html
    https://web.archive.org/web/20160924101625/https://wiki.centos.org/About/Product
    https://access.redhat.com/support/policy/updates/errata#Life_Cycle_Dates

[2024-04-30 edit]: Clarifies CentOS Stream lengths thanks to the help of @mattdm, who gently pointed out what was staring me in the face on pages I had already cited.

P.S. I am using the word "active" instead of "support" or "maintenance" since they can have specific meaning within a support window (e.g. bug fixes vs security patches, full support vs maintenance vs extended, etc.). I am doing this to help distinguish between paid "customer support" and the support of the software itself by programmers.

# History of Red Hat Enterprise Linux and Fedora

Red Hat first offered an enterprise Linux support subscription for Red Hat Linux 6.1. It was not a separate product, but the subscription offering was branded as Red Hat 6.2E. Subsequently, Red Hat started creating a separate product with commercial service level agreements and longer lifecyle based on Red Hat Linux, and later on Fedora.

| Release                      | Codename                    | Release Date    | Based on                                                   |
| ---------------------------- | --------------------------- | --------------- | ---------------------------------------------------------- |
| Red Hat Linux 6.2E           | Zoot                        | 2000-03-27      | Red Hat Linux 6.2                                          |
| Red Hat Enterprise Linux 2.1 | Pensacola (AS)/ Panama (ES) | 2002-03-26 (AS) | Red Hat Linux 7.2                                          |
| Red Hat Enterprise Linux 3   | Taroon                      | 2003-10-22      | Red Hat Linux 9                                            |
| Red Hat Enterprise Linux 4   | zNahant                     | 2005-02-15      | Fedora Core 3                                              |
| Red Hat Enterprise Linux 5   | Tikanga                     | 2007-03-14      | Fedora Core 6                                              |
| Red Hat Enterprise Linux 6   | Santiago                    | 2010-11-10      | Mix of Fedora 12 Fedora 13 and several modifications       |
| Red Hat Enterprise Linux 7   | Maipo                       | 2014-06-10      | Primarily Fedora 19 with several changes from 20 and later |
| Red Hat Enterprise Linux 8   | Ootpa                       | 2019-05-07      | Fedora 28                                                  |
| Red Hat Enterprise Linux 9   | Plow                        | 2022-05-17      | Fedora 34                                                  |
