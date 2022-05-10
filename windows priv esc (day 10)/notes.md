# WINDOWS ACCESS CONTROL MODEL


### Access Tokens (Users)
- roles
- permissions
- rights

### Security Descriptors (Files)
- DACL: Discretionary Access Control list
- SACL: System access control list
- ACES: Access Controls entries (specify what to audit on the file inside of the SACL)


# WINDOWS PRIVILEGE ESCALATION

Two main ways: scheduled tasks and services
- Binary relpacement is replacing the legitimate binary with a malicious binary
- DLL Hijacking is replacing the legitimate DLL that the binary is calling out to with a malicious DLL

## 1. Scheduled Tasks
- Binary Replacement
- DLL Hijacking


##### Scheduled Tasks Considerations

- Write Permissions

- Non-Standard Locations

- Unquoted Executable Paths

- Vulnerabilities in Executables

- Permissions to Run As SYSTEM



##### SCHEDULED TASKS DEMO

```

SETUP
--------------------------------------------------------------------------------------------------------------------------------------------
schtasks /create /TN Putty  /TR "C:\Putty\Putty.exe" /RU SYSTEM /SC onlogon		#create the schedule tasks

mkdir C:\Putty									#create directory called putty in the C:\ location


[putty download](https://www.chiark.greenend.org.uk/~sgtatham/putty/releases/0.67.html)

#download the putty.exe (ssh and telnet client itself) 32-bit version to winops
------------------------------------------------------------------------------------------------------------------------------------------------

```









## 2. Services
- Binary Replacement
- DLL Hijacking

### DLL Search 

C:\Windows
C:\Windows\system32

### UAC

Checking for the UAC settings:
`reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
>> `EnableLUA   REG_DWORD   0x1   `
                              ^--------0x1 indicates UAC on, 0x0 indicates UAC off


### File Manifest
- Requested Execution Level: asInvoker, highestAvailable, asAdministrator

Autoelevate: True or N/A


## FILE MANIFEST DEMO
 `net use * \\live.sysinternals.com\tools`		#map the sysinternals tools to the next available drive so you can use them
  `.\sigcheck.exe -m C:\Windows\System32\eventvwr.exe`	# outputs the file manifest for eventvwr.exe with absolute path
  
 
 ` .\sigcheck.exe -m C:\Windows\System32\eventvwr.exe | findstr /i autoelevate`		#find if the eventvwr.exe binary has autoelevate TRUE or FALSE
 
 `.\sigcheck.exe -m C:\Windows\System32\eventvwr.exe | findstr /i level`		#find the requested execution levels of the eventvwr.exe binary
 
 


