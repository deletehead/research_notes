# Research into LSA Protection: `RunAsPPL`
## What is PPL?
- `RunAsPPL` is an LSA protection introduced in Windows 8.1. See [Microsoft's article](https://docs.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection) introducing it.
- It can be configured via a reg entry set to DWORD `0x00000001`: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa`
- Persists across reboots and is a pain to get around. You will get "Access Denied" when doing things that touch LSASS such as making a `.dmp` from Task Manager and of course Mimikatz.
- During one internal pentest, it really frustrated me -- assuming it was the EDR tool blocking me, it took me a while to see it was PPL.
- Exceptions to this protection include plug-ins that are digitally signed with a MS signature and adherence to MS's SDL.
- The exceptions are intended to cover things like smartcard reader plugins, password filtering plugins, etc.
- This is in the Protected Store meaning even if you could modify the reg entry it would reset itself upon reboot.
- More info:
  - [T1177: LSASS Driver](https://attack.mitre.org/techniques/T1177/)
  - [CrowdStrike Blog: Evolution of Protected Processes](https://www.crowdstrike.com/blog/evolution-protected-processes-part-1-pass-hash-mitigations-windows-81/)

## Check for PPL
- Simple registry check shows if it's configured: 
  ```
  reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa /v RunAsPPL
  ```

## Bypasses
- Mimikatz has a bypass to load an MS-signed Windows kernel driver `Mimidrv`. See [this SpecterOps post](https://posts.specterops.io/mimidrv-in-depth-4d273d19e148) for more info.
- The `!processprotect` function is probably the most used functionalities of `mimidrv`.
  - The goal is to remove process protection from a process (typicall `lsass.exe`)
  - It will patch the process and remove process protections
  - You can also protect processes too with this, for instance, protect `mimikatz.exe` and then unprotect `lsass.exe`
  ```
  mmk# privilege::debug
  mmk# !+
  mmk# !processprotect /process:mimikatz.exe
  mmk# !processprotect /process:lsass.exe /remove
  ```

