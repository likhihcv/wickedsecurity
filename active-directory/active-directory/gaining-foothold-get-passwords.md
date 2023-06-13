---
description: Getting foot hold to at-least one machine is necessary to start within
---

# Gaining Foothold - Get Passwords

## Steps to get user credentials

* Do Information gathering to get email addresses(OSINT).
* Send them phishing emails to get their machine access in this way you can get hash or trick them to open payload.
* Weak Passwords - you can try password spraying in their publically available portal like outlook(EWS, OWA, EAS), cisco VPNlogin, Sealpath, or other endpoints that are integrated with Active Directory.
* LLMNR/NBTS Poisoning - responder inveigh.
* Default Passwords on Applications might give some more clues.
* Create a fake Corporate Wi-Fi (WPE/WPA-MGT) to get netNTLM hashes which you can Bruteforce them later.
* Ask the Admin to reset the password. so that you will able to figure the default password which admin suggests. helps in domain password spraying
* check user description property
* check accessible shares, you might find something sensitive.



