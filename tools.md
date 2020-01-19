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
  
5. Before copying it into an EFI partition be sure to check the hash using e.g. virustotal.com.

Explanation: According to Alex Ionescu, in his talk https://www.youtube.com/watch?v=nSqpinjjgmg, today's SmcFlasher.efi is a stripped-down version of the original SmcUtil.efi. So when messing with the SMC, you want to make sure you have the full-blown tool at your hands.

The DMG behind the webarchive link was obtained by performing the following browse sequence.

- www.apple.com ==> (In the top bar) "Support" ==> (scroll all the way down until bottom) "Downloads & Updates"
- https://support.apple.com/en_US/downloads ==> (in search field enter "smc") "MacBook Pro SMC Firmware Update 1.7" ==> "Download"
