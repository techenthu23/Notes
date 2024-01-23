# How to Find Your Windows 10 OEM Product Key

several tools (including the Command Prompt, PowerShell, NirSoft's Prodkey, and finding the Certificate of Authenticity) to find the product key for Windows. At least one of these methods can be used to recover the key for Windows 10, 8, 7 and the server versions of Windows. Though it's hidden in Window's Activation screen and inacccessbile in your computer's BIOS, you can retrieve an OEM key from the BIOS or UEFI, or obtain it from the registry. In case you want to copy and paste the commands, here is the one for the 

Command Prompt: 
wmic path softwarelicensingservice get OA3xOriginalProductKey 

and here is the one for PowerShell:
```powershell
(get-wmiobject -query ‘select * from SoftwareLicensingService’).OA3xOriginalProductKey
```


# How To – Linux find Windows 10/11 OEM product key
To find your original Windows 10/11 product key from Linux:
	1. Open the terminal application.
	2. You must run the Linux command as the root user.
	3. Type `sudo strings /sys/firmware/acpi/tables/MSDM` to print Windows 10/11 or Windows 8 OEM product key
	4. You can also use the acpidump command to get the same information under Linux.
Let us see all the commands and examples in detail to located Windows 10/11 OEM serial number or key.

# How to validate the OEM activation key in Windows 10
Applies to:   Windows 10 - all editions
Original KB number:   4346763

Background
Starting at Windows 10 Creators Update (build 1703), Windows activation behavior has changed. The unique OA3 Digital Product Key (DPK) isn't always presented as the currently installed key in the device. Instead, the system behaves as follows:
- Windows 10 (including all versions starting at Windows 10 Creators Update) is deployed to a device by having the appropriate default product key. You can run `slmgr /dli` or `slmgr /dlv` to show the partial default product key instead of the OA3 DPK as the current license in the firmware. The product ID displayed on the `Settings > System > About page` isn't unique for the Windows 10 key that's being used.

• A device that's running any Windows 10 OEM client edition, such as Windows Home or Windows Professional, and is activated by using the OA3 DPK in the firmware is upgraded to a newer version. For example, it's upgraded from build 1703 to build 1709. However, sometimes running `slmgr /dli` or `slmgr /dlv` doesn't show the OA3 DPK as the current license. Instead, these commands show the default product key.

The behavior is by design. The activation and user experience aren't affected. But OA validation in the factory may be affected as follows:
- The output of the `slmgr /dlv` or slmgr /dli command isn't necessarily the last five (5) digits of the injected DPK. So you can no longer rely upon these commands to return the expected results.

# Recommendations for validating the product ID against the product key ID of OA3 DPK

Every OEM has a different manufacturing process that has been adopted through years of experience. Specifically, to validate the DPK against the installed Windows 10 edition, we recommend that you don't rely on the output of `slmgr /dlv` or `slmgr/dli`. Instead, use the latest OA3Tool as follows:

- `OA3TOOL /Validate`
It runs a validation pass to make sure that:
	- the MSDM table exists.
	- the MSDM table header includes all the required fields.
	- the MSDM table entries exist and comply with the correct formats.

- `OA3TOOL /CheckEdition`
	Does a cross-check between the injected DPK and the target Windows edition if they match.

https://www.microsoft.com/en-in/software-download/windows11
