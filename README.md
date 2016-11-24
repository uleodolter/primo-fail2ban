# primo-fail2ban

Primo by Ex Libris provides an integrated IP-based DoS filter.  Configuration details can be found in Primo Back Office Guide:

https://knowledge.exlibrisgroup.com/Primo/Product_Documentation/Back_Office_Guide/110Additional_Primo_Features/005General_Configuration_Wizard#Security_Parameters

Fail2ban scans log files and bans IPs that show the malicious signs (http://www.fail2ban.org).

This fail2ban configuration tries to combine both to have a flexible DoS protection for Primo frontend servers.

## installation on Primo Frontend Server

You need root access on your Primo frontend server machine.

```
git clone https://github.com/uleodolter/primo-fail2ban.git
# adust jail.d/primo-jail.conf for your needs
ssh root@primo-fe-server yum install -y fail2ban
scp -p filter.d/primo-IPDosFilter.conf root@primo-fe-server /etc/fail2ban/filter.d/
scp -p jail.d/primo-jail.conf root@primo-fe-server /etc/fail2ban/jail.d/
ssh root@primo-fe-server service fail2ban start
```

## test Fail2ban "failregex" option 

```
fail2ban-regex log/library_server.log ./filter.d/primo-IPDosFilter.conf  -v

Running tests
=============

Use   failregex filter file : primo-IPDosFilter, basedir: .
Use      datepattern : ^Year-Month-Day 24hour:Minute:Second,Microseconds
Use         log file : log/library_server.log
Use         encoding : UTF-8


Results
=======

Failregex: 1 total
|-  #) [# of hits] regular expression
|   1) [1] ^ WARN  \[.+\] \[c-IPDosFilter\] \[.+\] - Attacking request\(info\): <HOST>_\d+, reqCounter = \d+ > \d+\s*$
|      192.168.3.58  Thu Nov 24 09:41:25 2016
`-

Ignoreregex: 0 total

Date template hits:
|- [# of hits] date format
|  [1] ^Year-Month-Day 24hour:Minute:Second,Microseconds
`-

Lines: 1 lines, 0 ignored, 1 matched, 0 missed
[processed in 0.00 sec]
```
