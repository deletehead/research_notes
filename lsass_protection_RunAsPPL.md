# Research into LSA Protection: `RunAsPPL`
## What is PPL?
- `RunAsPPL` is an LSA protection introduced in Windows 8.1. See [Microsoft's article](https://docs.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection).
- It can be configured via a reg entry set to DWORD `0x00000001`: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa`
- Persists across reboots and is a pain to 

## Bypasses
- Mimikatz has a bypass
