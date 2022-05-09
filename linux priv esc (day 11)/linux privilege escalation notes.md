[gtfo bins website](https://gtfobins.github.io)

- ignore anything about restricted shell
- literally copy paste the valid commands you find from performing `sudo -l
- super duper best friend for privesc`
- look at nonstandard binaries (gtfo bins wont help for these)
## SUDO STUFF

`sudo -l`	:	shows the commands you can run sudo with as current user. IF YOU SEE ANYTHING, TAKE NOTE!
`/etc/sudoers`	:	file containing all users with sudo access

- if you can sudo `cat`, you can crack passwords with john after looking into `/etc/shadow`!!

## SUID/GUID
- look for these set bits to pop root
- `find / -perm /4000 2>/dev/null`	:	returns any files with the SUID bit set
- `find / -perm /2000 2>/dev/null`	:	returns any files with the GUID bit set
- 

## CRONS

- look into netcat listeners, anything running under root, anything that looks out of the ordinary 

## INSECURE PERMISSIONS
- `echo $PATH`	:	shows your directory paths for binaries
- look for `.` in your path variable; can be exploited to run scripts with the same names as known binaries from your current workig directory
