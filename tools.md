# Insyde Software Corp (R) older BIOS Tools release: (Insider_BIOS_Tools):
Explanation: "https://firmwaresecurity.com/2017/09/06/insider_bios_tools-bios-tools-from-insyde-software/"

https://gitgud.io/sapp4ire/lnsider_Bl0S_TooIs/repository/master/archive.zip

https://web.archive.org/web/20191230150836/https://gitgud.io/sapp4ire/lnsider_Bl0S_TooIs/repository/master/archive.zip

(Pathetic attempt to remove tools after release: https://github.com/github/dmca/blob/master/2019/05/2019-05-02-Insyde.md)

# "old" SmcUtil:

1. Download https://web.archive.org/web/20200119132710/https://updates.cdn-apple.com/2019/cert/041-88046-20191011-2661ddb2-00fc-4279-862c-13b2aeec4a88/MacBookProSMCUpdate.dmg and save but do not open!

2. Mount the DMG and copy the "MacBookProSMCUpdate.pkg" to a folder.

3. Extract the PKG using e.g. "pkgutil --expand-full ./MacBookProSMCUpdate.pkg ./MacBookProSMCUpdate.pkg/extracted/"

4. Choose one of the contained PKG's and browse into it until you find "<some-thing>.smc" along with the "SmcFlasher.efi". "SmcFlasher.efi" is the renamed, full-blown "SmcUtil.efi".
  
5. Before copying it into an EFI partition be sure to check the hash using e.g. virustotal.com _and to read "Caveat"_.

Explanation: According to Alex Ionescu, in his talk https://www.youtube.com/watch?v=nSqpinjjgmg, today's SmcFlasher.efi is a stripped-down version of the original SmcUtil.efi. So when messing with the SMC, you want to make sure you have the full-blown tool at your hands.

The DMG behind the webarchive link was obtained by performing the following browse sequence at January 19, 2020 at roughly 12Z.

- www.apple.com ==> (In the top bar) "Support" ==> (scroll all the way down until bottom) "Downloads & Updates"
- https://support.apple.com/en_US/downloads ==> (in search field enter "smc") "MacBook Pro SMC Firmware Update 1.7" ==> "Download"

_Or https://support.apple.com/en-us/HT201518 being the other FW source...just make sure to stay below 2012 according to Ionescu's talk._

Caveat: You cannot use the "SmcUtil.efi" as is, since you must supply command line parameters to make use of it, and you must be able to load an EFI Fat binary. At the moment I cannot think of a simple way of satisfying both conditions at the same time. You could directly choose the SmcUtil.efi (albeit renamed into "BOOTX64.EFI") from the BDS but then it does not let you supply any command line arguments and just continues to boot into your default OS after maybe flickering your screen for some 0.1 seconds. You cannot use the EFI shell either since it doesn't let you open the SmcUtil.efi in the first place, at least not from what I tested at the command line. The reason for that is, EFI shell checks for the 'MZ' magic at the beginning of the file header. SmcUtil as an EFI Fat binary doesn't employ the 'MZ' magic at beginning of the file and is not recognized.

Hence, from the Fat binary you must somehow carve out that SmcUtil.efi which is suitable to your architecture. See http://refit.sourceforge.net/info/fat_binary.html for information on EFI Fat binaries. I did the carving manually using HexFiend hex editor. After carving the bytes, dumping them to a new file, and renaming that new file to BOOTX64.EFI you should be able to use the SmcUtil in EFI shell as usual. Once again, you are recommended to check the target file with virustotal.com.

# MacPmem
Get from (TBD)

Install using (TBD)

Load using "sudo kextload /Library/StagedExtensions/Users/micelwhave/Downloads/roootfolder/osxpmem.app/MacPmem.kext/"

Disable honoring EFI memory map using "sudo sysctl -w kern.pmem_allow_unsafe_operations=1" (this has to be redone each time that extension is loaded).

Dump n pages starting at page offset m using "sudo dd if=/dev/pmem of=/Users/micelwhave/minidump.dmp bs=4096 skip=m count=n"

CAVEAT: Apparently, reading entire pages from PCI space leads to wrongly dumped register values. Thus dump b0 d0 f0 like this:
```
sudo dd if=/dev/pmem of=/Users/micelwhave/0xe0000000.dmp bs=4 skip=$((0xe0000000/4)) count=1024
```

# Quick And Dirty Bare-Metal x86 Emulator
(This won't do you any good if your code can't cope with its execution starting at 0xfffffff0 in real mode.)

Install VBox 6.0

Create new VM in Expert Mode:

> *Microsoft Windows*

> *Windows 7 (64-bit)*

> RAM: *4096 MB*

> *Create a virtual hard disk now*

> Disk: *1024.00 MB*

> *VDI*

> *Fixed size*

Customize **System**, **Motherboard**

> Chipset: *ICH9*

> Extended Features: Check *IO APIC* and *EFI*, Uncheck *Hardware Clock in UTC Time*

Customize **System**, **Processor**

> Processor(s): *1 CPU*

> Extended Features: Uncheck *Enable PAE/NX* and *Enable Nested VT-x/AMD-V*

Customize **System**, **Acceleration**

> Paravirtualization Interface: None

> Hardware Virtualization: Uncheck *Enable Nested Paging*

Customize **Display, Screen**

> Video Memory: *64 MB*

> Graphics Controller: *VBoxVGA*

> Acceleration: Uncheck *Enable 3D Acceleration* and *Enable 2D Video Acceleration*

Customize **Storage**

> Remove the *Empty* optical drive and confirm its removal

> Uncheck *Use Host I/O Cache*

Run the VM to make sure the UEFI Shell does come up properly.

Backup the ROM image */Applications/VirtualBox.app/Contents/MacOS/VBoxEFI64.fd*, enter your sudo PW when asked.

Overwrite at maximum the last 0x100000 bytes of *VBoxEFI64.fd* with your payload, e.g. with the last 0x100000 bytes of your machine's SPI ROM dump.

Overwrite the first instruction at 0x1ffff0 (later mapped to 0xfffffff0 "Reset Vector") with 0xf4 (that is, a halt).

If you overwrote an instruction that was multiple bytes long pad the remaining bytes of that instruction with 0x90s.

Run the VM you created before by issuing following command:

```
/Applications/VirtualBox.app/Contents/MacOS/VBoxManage startvm "<your VM>" -E VBOX_GUI_DBG_AUTO_SHOW=true -E VBOX_GUI_DBG_ENABLED=true
```
Activate the machine window and goto *Machine* ==> *Pause* which unpauses the VM.

In the debugger window write *stop* to halt execution.

Now start typing *t* several times to single-step your x86 code.

After your code started using a GDT you may type *dg* to display it.

CAVEATS:

You may encounter the following error albeit with the address of your code:

> u: error: DBGCCmdHlpVarToDbgfAddr failed on 'f000:0000f562 L 0': VERR_INVALID_SELECTOR

For me, the error didn't seem to harm to the execution flow. It seems to disappear after switching into 32-bit protected mode. Judging from the "u" it is likely just the disassembler which is complaining here.

Furthermore, breakpoints didn't work for me, neither *bp* nor *br*. The reason for that might be that the CPU obviously isn't configured at all upon starting execution at the reset vector...
