# Network

### Common Errors and Fixes

### Unable to install pip2

```
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python2.7 get-pip.py
```

### SSL Errors

```
-k with curl
export PYTHONHTTPSVERIFY=0 //for python urllib
echo "CipherString=DEFAULT@SECLEVEL=1" >> /etc/ssl/openssl.cnf
```

### pip egg\_info error

Distribute has been merged into Setuptools as of version 0.7. If you are using a version <=0.6, upgrade using **pip install --upgrade setuptools** or easy\_install -U setuptools.

### This version of c:\inetpub\wwwroot\JuicyPotato.exe is not compatible with the version of Windows you're running. Check your computer's system information and then contact the software publisher.

transfer file in binary mode through FTP -> **binary**



**is\_known\_pipename / EternalRED / Sambacry**

set SMB::AlwaysEncrypt false \
set SMB::ProtocolVersion 1



### run python2 code in python3

pip install virtualenv \
virtualenv -p /usr/bin/python2.7 venv # or wherever your python2.7 resides \
source venv/bin/activate.sh
