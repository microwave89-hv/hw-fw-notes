# IED*, Intel Enhanced Debug, ...
What is it?

Why is IEDRAM supposed to be in SMRAM?

When was it introduced?

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

_Broadwell Technology is supposedly capable of this. (https://web.archive.org/web/20200107052346/https://software.intel.com/sites/default/files/managed/0c/92/STM_User_Guide-001.pdf, p. 8, p. 12)_

Would x86-64 handlers be possible? Do such handlers currently exist in SMRAM?

Would it be possible for such handlers to access DRAM up to TOM?

_For handlers that employ paging it should be possible._

# SMC
What is handled in SMC and what in SMM?

# STM
~~Secure~~ Transfer Monitor or SM ~~*~~ I Transfer Monitor?

Would IVB & PPT be ready for it? Is it actively used on Macbook Pro 10,1?

How does it relate to Ring -1?

Where is MSEG supposed to reside?

# What is TXT? (Trusted Execution ~~* (* =~~ Technology ~~?)~~)
Does it relate to STM? To VMM? To SMM?

What does mean "bits, lockable by TxT"?

# Mapping
What is "reclaim"?

Where is the IOMMU?

Can PCI config space be accessed by a mere "mov xcx, [desired address]"?

If SMRAM is open, can it be accessed by MacPmem? By fmem? "mov xcx, [desired address]"?

# Sway
Is there any situation in which -1 <= SMM?

Does the SMC play any noticeable role? Would it be possible?

How dangerous is the GbE FW?

How dangerous is the ME on a MacBook Pro 10,1?

# ME
How is MESEG being used?

Would MESEG be accessible from -2? Is it even worth it?

Would it be possible for the MESEG to reside in DRAM < 0xffffffff?

What does the pin strap do which goes to the SMC?

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

# SMM Security
What was the timeline of attacks, and how were they mitigated?
