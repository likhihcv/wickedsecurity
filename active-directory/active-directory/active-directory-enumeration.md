---
description: Enumerating Active Directory
---

# Enumeration

## Current Domain info

info like Forest, DomainControllers

```
[System.DirectoryServices.ActiveDirectory.Domain]::GetComputerDomain()
```

## Enumeration Tools

Powerview - [https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)

Microsoft AD Module - [https://github.com/samratashok/ADModule](https://github.com/samratashok/ADModule)\
[**https://docs.microsoft.com/en-us/powershell/module/addsadministration/?view=win10-ps**](https://docs.microsoft.com/en-us/powershell/module/addsadministration/?view=win10-ps)\
**import** Microsoft.ActiveDirectory.Management**.dll and** ActiveDirectory**.psd1**

Here Upper command is PowerView\
Lower Command is AD Module

## Domain

### Get current domain

```
Get-NetDomain

Get-ADDomain
```

### Get Object of another domain(if trusts)

```
Get-NetDomain –Domain moneycorp.local

Get-ADDomain -Identity moneycorp.local
```

### Get domain SID for the current domain

```
Get-DomainSID

(Get-ADDomain).DomainSID
```

## Policy

### Get domain policy for the current domain&#xD;

```
Get-DomainPolicy
(Get-DomainPolicy)."System Access"
(Get-DomainPolicy)."Kerberos Policy"
```

### Get domain policy for another domain

```
(Get-DomainPolicy –domain eurocorp.local)."system access"
```

### Get domain controllers for the current domain and it's IP

```
Get-NetDomainController

Get-ADDomainController
```

### Get domain controllers for another domain

```
Get-NetDomainController -Domain eurocorp.local

Get-ADDomainController -DomainName eurocorp.local -Discover
```

## Users

### Get a list of users in the current domain

```
Get-NetUser
Get-NetUser –Username student1

Get-ADUser -Filter * -Properties *
Get-ADUser -Identity student1 -Properties *

net user /domain
net user student82 /domain
```

### Get List of all properties for users in the current domain

pwdlastset, badpwdcounts property helps in understanding if the account id honeypot or decoy account\
users have bad passwords and pwdlastset is very old for decoy accounts

```
Get-UserProperty
Get-UserProperty –Properties pwdlastset

Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -MemberType *Property | select Name
Get-ADUser -Filter * -Properties * | select name,@{expression={[datetime]::fromFileTime($_.pwdlastset)}}
```

### Search for a particular string in a user's attributes:

```
Find-UserField -SearchField Description -SearchTerm "built"
Get-ADUser -Filter 'Description -like "*built*"' -Properties Description | select name,Description
```

## Computers

### Get a list of computers in the current domain&#xD;

These could be actual computes or VMs\
Ping ICMP Request is used to detect if host is online . If ICMP packets are didabled in firewall it could give false positives that machine is offline

```
Get-NetComputer
Get-NetComputer –OperatingSystem "*Server 2016*"
Get-NetComputer -Ping
Get-NetComputer -FullData
Get-NetComputer -FullData | select operatingsystem

Get-ADComputer -Filter * | select Name
Get-ADComputer -Filter 'OperatingSystem -like "*Server 2016*"' -Properties OperatingSystem | select Name,OperatingSystem
Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}
Get-ADComputer -Filter * -Properties *
```

## Groups

### Get all the groups in the current domain

```
Get-NetGroup
Get-NetGroup –Domain <targetdomain>
Get-NetGroup –FullData


Get-ADGroup -Filter * | select Name
Get-ADGroup -Filter * -Properties *

net group  /domain
net group "RDPUsers" /domain
```



### Get all groups containing the word "admin" in group name

```
Get-NetGroup -GroupName *admin*
Get-NetGroup -GroupName *admin* -Domain eurocorp.local

Get-ADGroup -Filter 'Name -like "*admin*"' | select Name
```

### Get all the members of the Domain Admins group

RID 500 denotes administrator

```
Get-NetGroupMember -GroupName "Domain Admins" -Recurse
Get-NetGroupMember -GroupName "Domain Admins" -Domain eurocorp.local

Get-ADGroupMember -Identity "Domain Admins" -Recursive
```

### Get the group membership for a user:&#x20;

RID 513 denotes a built-in group

```
Get-NetGroup –UserName "student1"

Get-ADPrincipalGroupMembership -Identity student1
```

### List all the local groups on a machine (needs administrator privs on nondc machines)

```
Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -ListGroups

show membership of administrator groups
Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local 
```

### Get members of all the local groups on a machine (needs administrator privs on non-dc machines)&#xD;

```
Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Recurse
```

### Get actively logged users on a computer (needs local admin rights on&#xD; the target)

```
Get-NetLoggedon -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Verbose
```

### Get locally logged users on a computer (needs remote registry on the target - started by-default on server OS)

```
Get-LoggedonLocal -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```

### Get the last logged user on a computer (needs administrative rights and&#xD; remote registry on the target)

```
Get-LastLoggedOn –ComputerName <servername>
```

## Shares

### Find shares on hosts in current domain.

```
Invoke-ShareFinder –Verbose
```

### Find sensitive files on computers in the domain&#xD;

```
Invoke-FileFinder –Verbose
```

### Get all fileservers of the domain&#xD;

enumerates high-value targets

```
Get-NetFileServer
```

## GPO

### Get list of GPO in current domain

Get-WindowsOptionalFeature -Online -FeatureName \
Enable-WindowsOptionalFeature -Online -FeatureName RSAT\
Install-WindowsFeature -Name GPMC\
Import-Module grouppolicy



```
Get-NetGPO
Get-NetGPO -ComputerName dcorpstudent1.dollarcorp.moneycorp.local

Get-GPO -All (GroupPolicy module)
Get-GPResultantSetOfPolicy -ReportType Html -Path
C:\Users\Administrator\report.html (Provides RSoP)
```

### Get GPO(s) which use Restricted Groups or groups.xml for interesting&#xD; users

List  the restricted groups, if get access to one of the groups we can access all others as it might be pushed to all the machines

```
Get-NetGPOGroup
```

### Get users which are in a local group of a machine using GPO

```
Find-GPOComputerAdmin –Computername dcorpstudent1.dollarcorp.moneycorp.local
```

### Get machines where the given user is member of a specific group

```
Find-GPOLocation -UserName student1 -Verbose
```

## Get OUs in a domain

```
Get-NetOU 
Get-NetOU -FullData

Get-ADOrganizationalUnit -Filter * -Properties * 
```

### Get GPO applied on an OU. Read GPOname from gplink attribute from&#xD; Get-NetOU&#xD;

gplink in get-netou is entered in -gponame

```
Get-NetGPO -GPOname "{AB306569-220D-43FF-B03B83E8F4EF8081}" 

Get-GPO -Guid AB306569-220D-43FF-B03B-83E8F4EF8081
(GroupPolicy module)
```

## Get the ACLs associated with the specified object

```
Get-ObjectAcl -SamAccountName student82 -ResolveGUIDs


```

### Get the ACLs associated with the specified prefix to be used for search

```
Get-ObjectAcl -ADSprefix 'CN=student82,CN=Users' -Verbose
```

### We can also enumerate ACLs using ActiveDirectory module but without&#xD; resolving GUIDs

### Get the ACLs associated with the specified LDAP path to be used for search

```
Get-ObjectAcl -ADSpath "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose
```

### &#x20;Search for interesting ACEs

```
Invoke-ACLScanner -ResolveGUIDs
```

### Get the ACLs associated with the specified path

```
Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"
```

## Trusts

### Get a list of all domain trusts for the current domain

```
Get-NetDomainTrust
Get-NetDomainTrust -Domain eurocorp.local

Get-ADTrust
Get-ADTrust -Identity eurocorp.local
```

### Get details about the current forest

```
Get-NetForest
Get-NetForest –Forest eurocorp.local

Get-ADForest
Get-ADForest –Identity eurocorp.local
```

### Get all domains in the current forest

```
Get-NetForestDomain
Get-NetForestDomain –Forest eurocorp.local

(Get-ADForest).Domains 
```

### Get all global catalogs for the current forest

```
Get-NetForestCatalog
Get-NetForestCatalog –Forest eurocorp.local

Get-ADForest | select -ExpandProperty GlobalCatalogs
```

### Map trusts of a forest&#xD;

maps trust between forests, not domains or child entities.so no output

```
Get-NetForestTrust
Get-NetForestTrust –Forest eurocorp.local

Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'
```

### Enumerating computers from the trustee domain

Map domain trust

## Sessions

### Find all machines on the current domain where the current user has&#xD;local admin access&#xD;

This function queries the DC of the current or provided domain for a list of computers **(Get-NetComputer)** and then use multi-threaded **Invoke-CheckLocalAdminAccess** on each machine.



```
 Find-LocalAdminAccess -Verbose
```

This can also be done with the help of remote administration tools like WMI and PowerShell remoting. Pretty useful in cases ports (RPC and SMB) used by **Find-LocalAdminAccess** are **blocked.**

### Find local admins on all machines of the domain (needs administrator&#xD; privs on non-dc machines).

This function queries the DC of the current or provided domain for a list of computers **(Get-NetComputer)** and then use multi-threaded **GetNetLocalGroup** on each machine.

```
Invoke-EnumerateLocalAdmin –Verbose
```

### Find computers where a domain admin (or specified user/group) has sessions

This function queries the DC of the current or provided domain for members of the given group (Domain Admins by default) using **Get-NetGroupMember,** gets a list of computers (**Get-NetComputer**) and list sessions and logged on users (**GetNetSession/Get-NetLoggedon**) from each machine.

```
Invoke-UserHunter
Invoke-UserHunter -GroupName "RDPUsers"

to check admin access
Invoke-UserHunter -CheckAccess
```

### Find computers where a domain admin is logged-in.

performs only on high valued targets

This option queries the DC of the current or provided domain for members of the given group (Domain Admins by default) using **Get-NetGroupMember**, gets a list _only_ of high traffic servers (DC, File Servers and Distributed File servers) for less traffic generation and list sessions and logged on users (**Get-NetSession/Get-NetLoggedon**) from each machine.

```
Invoke-UserHunter -Stealth
```

### Get actively logged users on a computer (needs local admin rights on&#xD; the target)

```
Get-NetLoggedon -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Verbose
```

### Get locally logged users on a computer (needs remote registry on the target - started by-default on server OS)

```
Get-LoggedonLocal -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```

### Get the last logged user on a computer (needs administrative rights and&#xD; remote registry on the target)

```
Get-LastLoggedOn –ComputerName <servername>
```

## Defense

Netcease is a script which changes permissions on the NetSessionEnum method by removing permission for Authenticated Users group.&#x20;

&#x20;This fails many of the attacker's session enumeration and hence user hunting capabilities. .\NetCease.ps1 \
.\NetCease.ps1 -revert

&#x20;Another interesting script from the same author is SAMRi10 which hardens Windows 10 and Server 2016 against enumeration which uses SAMR protocol (like net.exe) • [https://gallery.technet.microsoft.com/SAMRi10-Hardening-Remote48d94b5b](https://gallery.technet.microsoft.com/SAMRi10-Hardening-Remote48d94b5b)

## find for sensitive texts like pass, password,etc in all files

search files in recycle bin also from shares

```
findstr.exe /spin "password" *.*
```

There are various ways of locally escalating privileges on Windows box: \
– Missing patches \
– Automated deployment and AutoLogon passwords in clear text \
– AlwaysInstallElevated (Any user can run MSI as SYSTEM) \
– Misconfigured Services \
– DLL Hijacking and more \
• We can use below tools for complete coverage \
– PowerUp: [https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc) \
– BeRoot: [https://github.com/AlessandroZ/BeRoot](https://github.com/AlessandroZ/BeRoot) .\beRoot.exe\
– Privesc: [https://github.com/enjoiz/Privesc](https://github.com/enjoiz/Privesc) Invoke-PrivEsc\
– WinPEAS: [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)

Services Issues using PowerUp\
• Get services with unquoted paths and a space in their name.\
Invoke-AllChecks\
Get-ServiceUnquoted -Verbose\
• Get services where the current user can write to its binary path or&#x20;change arguments to the binary\
Get-ModifiableServiceFile -Verbose\
• Get the services whose configuration current user can modify.\
Get-ModifiableService -Verbose

## Feature Abuse

You can miss-use Enterprise applications like Jenkins which mostly have admin privileges and find a way to run commands

## Without logging in we can list users



## Bloodhound

Bloodhound represents AD entities and relationships in the form of GUI

bloodhound requires ne04j\
use suitable version v3 and v2 are incompatible to each other

upload zip files in bloodhound start mapping\
you can use built-in queries\
you can mark users as owned \
you can find paths,etc

```
Invoke-BloodHound -CollectionMethod All -Verbose
Invoke-BloodHound -CollectionMethod LoggedOn -Verbose

Invoke-BloodHound -CollectionMethod All -ExcludeDC    //less noisy
```

