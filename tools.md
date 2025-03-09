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

# Skimming ASCII formats like Intel Hex/Motorola Hex/SREC/TBD for strings

In general there are 2 methods:
1. Q'n'd:
  * Delete header sums/hash lines
  * If the file features separate data addresses, such as in an Apple .smc file, find/replace by no data as much of the address and its descriptor as you can such that none of the non-zero digits of the address is deleted *and* no odd number of digits is left in the data line
  * Then find/replace all line descriptions including their separators by no data
  * If there are any separators left delete them now
  * Paste the resulting file, which by now should only contain hex-digits, to a hex viewer
  * Skim the file for telltale strings
  
2. Less q'n'd: (Idea from InversePath researchers in their 2012 talk "Practical Exploitation of Embedded Systems" (https://dev.inversepath.com/download/public/embedded_systems_exploitation.pdf)), with the Apple .smc file as an example:
  * `$ grep -o -e "[A-Fa-f0-9]\{128\}" 2012MBPR15.smc | xxd -r -p > 2012MBPR15be.bin` (aka "find hex digits, and *iff* found 128 consecutive of them that is a match. Then print only the matching part (the 128 consecutive nibbles) and pipe the output into hex dump tool") (Note that this works only if the payload (data) lines are the longest consecutive hex data in that file, such as in an Apple .smc file!)

TL;DR: Remove at least all non-hex digits, __but be extremely cautious when using find/replace to remove line descriptions and separators! If you accidentally remove or leave in there any odd number of hex digits (nibbles!) the file may become entirely uninterpretable when viewed in a hex viewer such as Hex Fiend.__
I.e. all data that follows after the deletion becomes unreadable until the next odd number of nibbles are deleted. Depending on where and how often that happens the resulting file may appear to have "no human-readable strings at all" or just "little human-readable strings".

# How disassemble.io in Intel unreal mode (reset vector & co.)

  * Arch: i386
  * Base Address: (some address that makes sense)
  * Mode: i8086 or i386 (Doesn't seem to have any effect?)
  * Address Size: addr16
  * Data Size: data16
  
  Disasm verification: 
  ```
  DB E3 0F 6E C0 66 31 C0 8E C0 8C C8 8E D8 B8 00 F0 8E C0 67 26 A0 F0 FF 00 00 3C EA 75 0F B9 1B 00 0F 32 F6 C4 01 74 41 EA F0 FF 00 F0 B0 01 E6 80 66 BE 8C FD FF FF 66 2E 0F 01 14 0F 20 C0
  ``` 
  loaded @ 0xfffff51c =
  ```
  fffff51c db e3                            fninit 
  fffff51e 0f 6e c0                         movd   mm0,eax
  fffff521 66 31 c0                         xor    eax,eax
  fffff524 8e c0                            mov    es,ax
  fffff526 8c c8                            mov    ax,cs
  fffff528 8e d8                            mov    ds,ax
  fffff52a b8 00 f0                         mov    ax,0xf000
  fffff52d 8e c0                            mov    es,ax
  fffff52f 67 26 a0 f0 ff 00 00             addr32 mov al,es:0xfff0
  fffff536 3c ea                            cmp    al,0xea
  fffff538 75 0f                            jne    0xfffff549
  fffff53a b9 1b 00                         mov    cx,0x1b
  fffff53d 0f 32                            rdmsr  
  fffff53f f6 c4 01                         test   ah,0x1
  fffff542 74 41                            je     0xfffff585
  fffff544 ea f0 ff 00 f0                   jmp    0xf000:0xfff0
  fffff549 b0 01                            mov    al,0x1
  fffff54b e6 80                            out    0x80,al
  fffff54d 66 be 8c fd ff ff                mov    esi,0xfffffd8c
  fffff553 66 2e 0f 01 14                   lgdtd  cs:[si]
  fffff558 0f 20 c0                         mov    eax,cr0
  ```
# Find EFI ROM version from macOS Recovery
(There you neither have eficheck, nor system_profiler, nor System Information, nor sys_diagnose, nor xargs, nor xxd, nor hexdump...)

1. Find raw ROM info
  * Open Utilities --> Terminal
  * ```# - ioreg -b -i -l -p IODeviceTree | grep -i rom```
  * Cmd+c to temporarily save the hex string after "apple-rom-info"
2. Interpret hex string as chars
  * ```# echo <hex string in quotes> | sed 's/\(..\)/\\x\1/g' | while IFS= read -r hex; do printf "%b" "$hex"; done```
 
