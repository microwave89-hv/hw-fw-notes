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

Caveat: You cannot use the "SmcUtil.efi" as is, since you must supply command line parameters to make use of it, and you must be able to load an EFI Fat binary. At the moment I cannot think of a simple way of satisfying both conditions at the same time. You could directly choose the SmcUtil.efi (albeit renamed into "BOOTX64.EFI") from the BDS but then it does not let you supply any command line arguments and just continues to boot into your default OS after maybe flickering your screen for some 0.1 seconds. You cannot use the EFI shell either since it doesn't let you open the SmcUtil.efi in the first place, at least not from what I tested at the command line. The reason for that is, EFI shell checks for the 'MZ' magic at the beginning of the file header. SmcUtil as an EFI Fat binary doesn't employ the 'MZ' magic at beginning of the file and is not recognized.

Hence, from the Fat binary you must somehow carve out that SmcUtil.efi which is suitable to your architecture. See http://refit.sourceforge.net/info/fat_binary.html for information on EFI Fat binaries. I did the carving manually using HexFiend hex editor. After carving the bytes, dumping them to a new file, and renaming that new file to BOOTX64.EFI you should be able to use the SmcUtil in EFI shell as usual. Once again, you are recommended to check the target file with virustotal.com.
