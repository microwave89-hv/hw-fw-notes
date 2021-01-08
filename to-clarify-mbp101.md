# IED*, Intel Enhanced Debug, ...
* What is it?

* Why is IEDRAM supposed to be in SMRAM?

* When was it introduced?

Does it have any use nowadays?

How does IEDRAM look like?

# DSC Structure
What is the DSC structure @xoreaxeaxeax was sinkholing?

How does it relate to the CPU State Save Map? To the full SMRAM?

Has it changed over the past few years? How?

Is it related in any way to EFI_SMRAM_DESCRIPTOR, or EFI_MMRAM_DESCRIPTOR?

# SMRAM
How does the entire SMRAM look like?

If chipsec tells something about CSEG and TSEG being configured properly, are there multiple zones of SMRAM that are equally protected?

If TSEG is supposed to be enabled, is TSEG access governed by the same D_XYZ flags that control CSEG access?

If both CSEG and TSEG are enabled are there handlers in both regions?

How does SMBASE relate to TSEG? To TSEGMB? Is it possible that SMBASE != TSEGMB if TSEG is enabled?

# SMI
Is the MMU capable of accessing paging structures in SMRAM? While CPU is in SMM?

A: Broadwell Technology is supposedly capable of this. (https://web.archive.org/web/20200107052346/https://software.intel.com/sites/default/files/managed/0c/92/STM_User_Guide-001.pdf, p. 8, p. 12)

Would x86-64 handlers be possible? Do such handlers currently exist in SMRAM?

Would it be possible for such handlers to access DRAM up to TOM?

A: For handlers that employ paging it should be possible.

# SMC
What is handled in SMC and what in SMM?

# STM
~~Secure Transfer Monitor or~~ SM ~~*~~ I Transfer Monitor ~~?~~

Would IVB & PPT be ready for it? Is it actively used on Macbook Pro 10,1?

How does it relate to Ring -1?

Where is MSEG supposed to reside?

A: MSEG resides in the Top of TSEG. (https://web.archive.org/web/20200107052346/https://software.intel.com/sites/default/files/managed/0c/92/STM_User_Guide-001.pdf, p. 11)

# What is TXT? (Trusted Execution ~~* (* =~~ Technology ~~?)~~)
Does it relate to STM? To VMM? To SMM?

A: It is a technology for measured launch, whose promises don't depend on the integrity of previously launched SW, or FW. When using STM, it virtualizes the SMI handler. The STM is executed with SMM rights, and resides in SMRAM. (https://web.archive.org/web/20200107052346/https://software.intel.com/sites/default/files/managed/0c/92/STM_User_Guide-001.pdf, pp. 8-9)

What does mean "bits, lockable by TxT"?

(What does mean "bits, lockable by LT"? What is LT?)

# Mapping
What is "reclaim"?

Where is the IOMMU?

Can PCI config space be accessed by a mere "mov xcx, [desired address]"?

A: According to the original PCI spec, the config space can only be read by 0xcf8/0xcfc config cycles.
There is however, the PCI-E spec which introduces the memory-mapped PCI configuration.
With the *MBP101.00F6.B00*-FW, the configuration space starts at memory address 0xe0000000, and it is
pointed to by the PCIEXBAR register.
The range from 0xe0000000 includes some (all?) config spaces of the old PCI devices.
For instance, the "old" config space of B/D/F 0/0/0 can be read by simply typing "mem 0xe0000000" in the EFI Shell.
Hence, odds are that the new configuration space and thus the old too, can be accessed by a mere "mov xcx, [desired address]".

If SMRAM is open, can it be accessed by MacPmem? By fmem? "mov xcx, [desired address]"?

A: As TSEG usually (always?) resides in "Low Usable DRAM" it should at least be readable in unreal mode by raw memory dereference.
It seems that there must exist a page mapping to access it from long mode. In EFI Shell there is a transparent mapping so it might be readable from there. EFI Shell doesn't honor the memory map and as such attempts to read even reserved regions (though the machine sometimes hangs/crashes when doing this). As long as the MacPmem or fmem modules don't honor the memory map the BIOS has handed over to the OS, the SMRAM should technically be accessible. Put in other words, if even the PCI-E config space is readable which is not even a DRAM access, why shouldn't then the TSEG be accessible, itself residing in perfectly "true" DRAM?


# Sway
Is there any situation in which -1 <= SMM?

Does the SMC play any noticeable role? Would it be possible?

How dangerous is the GbE FW?

A: A first analysis has shown that at least in the MBP101.00F6.B00 there doesn't appear to be GbE FW in the first place.

How dangerous is the ME on a MacBook Pro 10,1?

# ME
How is MESEG being used?

Would MESEG be accessible from -2? Is it even worth it?

Would it be possible for the MESEG to reside in DRAM < 0xffffffff?

What does the pin strap do which goes to the SMC?

What is the minimum of (ME) FW or HW straps to exist for the ME to not reset the platform after 30(?) min?

# Current FW MBP101.00F6.B00
Are all issues solved related to S3? IIRC there was one vuln which was only solvable by a new MacBook Pro?

Why does chipsec fail with regards to several flash protection features?

What role do the misconfigured protections on some EFI vars play?

SMM_BWP does exist on PPT but is not reported to be used. Why hasn't the FW been updated in light of Speed Racer?

Why is the ME reported to be in MFG mode?

What is roughly the percentage of Intel(R) Reference Code? EDK 1.10? edk2? Apple Inc. added value?

Why can be found at least 1 reference to Insyde(R) in the FW?

Does it use undocumented MSR's? Which? In which way?

Please draw two memory maps, one from the host view, and one from DRAM view! Where does it not correlate to setup by MRC?

Which version of the ACPI Specification is used?

A: In the (first) FADT (FACP) referenced from the XSDT there is a "4" at offset 8. So most likely the proper spec is https://uefi.org/sites/default/files/resources/ACPI_4_Errata_A.pdf, or https://uefi.org/sites/default/files/resources/ACPI_4.pdf. What's a little funny though, the "AppleACPIPlatform" kext appears to be derived from at least ACPI 6.0. This would mean the OS driver might demand features which the underlying platform FW doesn't support.

# SMM Security
What was the timeline of attacks, and how were they mitigated?

Boot Guard? PFAT? STM? TXT? Bios Guard?

# FW Structures
What does the EFI CAPSULE "MBP101_00F6_B00.scap" consist of?

What does an FW Volume contain? Where are they in a CAPSULE? In the SPI ROM dump?

What is an FFS and where are such in a CAPSULE? In the SPI ROM dump?

Where in the CAPSULE is GbE FW? ME FW? BIOS + reset vector?

Where in the SPI ROM dump is GbE FW? ME FW? BIOS + reset vector?

# Reproducibility of SW-based SPI ROM Dumps
How do the ROM contents change between two normal boots?

What parts do change?

How does it change upon altering the keyboard lighting or further Macbook Pro features between two reboots?

Does it change upon invoking legacy media? What?

# Reproducibility of HW-based SPI ROM Readings
How do these unfettered readings compare to SW-based dumps?

Any unexplainable differences that might pinpoint BIOS malware? ;) 

# Various Questions
Where does the BOOTLOG file in the root dir of the EFI volume stem from? And what is that "SlingShot" which allegedly is logging something?

A: BOOTLOG appears to be created by the SlingShot service/driver/app, which for me has the GUID D5B366C7-DB85-455F-B50B-900A694E4C8C (kudos to UefiTool_ne authors!). The invocation of the SlingShot image has been correllating to at least the use of the Recovery Mode activated by pressing and holding Cmd + R.
Sample output might read:
> SlingShot: Starting Local and Network Diags/Recovery Session at 01/27/2020  18:22.

What is "Merlin" in the Intel(R) Reference Code of the SEC phase?

What is __Firmware.scap__ and why is it that the UefiTool_ne cannot open it properly? Or if it *is* opening this properly, why can't I spot human-readable strings??
And *why* is __Firmware.scap__ that large, and clearly different from e.g. the MBP101.00F6.B00???
