# IED*, Intel Enhanced Debug, ...
What is it?

Why is IEDRAM supposed to be in SMRAM?

When was it introduced?

Does it have any use nowadays?

What does IEDRAM look like?

# DSC Structure
What is the DSC structure @xoreaxeaxeax was talking about?

How does it relate to the CPU State Save Map? To the full SMRAM?

How did it change over the last years?

Is it related in any way to EFI_SMRAM_DESCRIPTOR, or EFI_MMRAM_DESCRIPTOR?

# SMRAM
How does the entire SMRAM look like?

If chipsec tells something about CSEG and TSEG, are there multiple zones of SMRAM that are equally protected?

If TSEG is supposed to be enabled, is TSEG access governed by the same D_XYZ flags that control CSEG access?

If both CSEG and TSEG are enabled are there handlers in both regions?

How does SMBASE relate to TSEG? To TSEGMB? Is it possible that SMBASE != TSEGMB if TSEG is enabled?

# STM
Secure Transfer Monitor or SM* Transfer Monitor?

Would IVB & PPT be ready for it? Is it actively used on Macbook Pro 10,1?

How does it relate to Ring -1?

# What is TXT? (Trusted Execution *(Technology?))
Does it relate to STM? To VMM? To SMM?

# Mapping Jargon
What is "reclaim"?

Where is the IOMMU?

# Sway
Is there any situation in which -1 <= SMM?
