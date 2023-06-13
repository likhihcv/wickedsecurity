# Port 123 - NTP

## NTP DoS reflection attacks

```
ntpq -c rv <IP>
ntpdc -c sysinfo <IP>
ntpdc -n -c monlist <IP>

nmap -sU -pU:123 -Pn -n --script=ntp-monlist <IP>
```





#### Links

{% embed url="https://cert.litnet.lt/2015/05/ntp-dos-reflection-attacks/" %}

{% embed url="https://resources.infosecinstitute.com/topic/network-time-protocol-ntp-threats-countermeasures/#gref" %}

