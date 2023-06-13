# Shares SMB, NFS

## Enumerate Shares

```
smbclient -L //[ip]
smbclient -L ////[ip]
smbmap -H [ip] -P [port] -R 
showmount -e [ip]
```

## Connect&#x20;

```
smbclient //[ip]/[share]
```

## Mount

```
mount -t cifs //10.10.10.134/Backups/ /opt/HTB/bastion/vhd -o ro
mount -o nolock 10.10.10.134:/Backups/ /opt/HTB/bastion
```

To solve protocol negotiation failed error in smbclient

EDIT edit /etc/samba/smb.conf >> under \[globals] paste this >>>\
client min protocol = CORE\
client max protocol = SMB3
