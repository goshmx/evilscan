evilscan
===============

[![Build Status](https://secure.travis-ci.org/eviltik/evilscan.png)](http://travis-ci.org/eviltik/evilscan)

[![NPM](https://nodei.co/npm-dl/evilscan.png)](https://nodei.co/npm-dl/evilscan/)

Massive ip/port scanner (tcp connect). Work in progress.

Status
-------
This program is young. I'm not a js expert. Feel free to fork and pull request !
I'm on #evilprobe (freenode) times to times.

Currently support :

* individual IP or IP range scan
* individual port, ports list, or ports range
* banner grabbing (not fully implemented, works with verbose ports only)
* IAC negotiation
* reverse dns
* geolocation information
* shell or json output
* optional progress details


Roadmap
--------
(see github issues and changelog.md)
* more and more tests
* udp scan
* syn scan


Install
-------

>Travis-ci tests (using mocha+chai), tell us it's ok using **nodejs >= 0.9**. So you can install evilscan using npm, as usual :
>```
>npm install -g evilscan
>```


Use evilscan as a node module
-----------
```
var evilscan = require('./');

var options = {
    target:'127.0.0.1',
    port:'21-23',
    status:'TROU', // Timeout, Refused, Open, Unreachable
    banner:true
};

var scanner = new evilscan(options);

scanner.on('result',function(data) {
        // fired when item is matching options
            console.log(data);
});

scanner.on('error',function(err) {
        throw new Error(data.toString());
});

scanner.on('done',function() {
        // finished !
});

scanner.run();

```

Use evilscan as a command line tool
-----------
Usage: evilscan <fqdn|ipv4|cidr> [options]
Example:
```
root@debian:~# evilscan 192.168.0.0/24 --port=21-23,80
```


Options
-------
```
  --port          port(s) you want to scan, examples:
                  --port=80
                  --port=21,22
                  --port=21,22,23,5900-5902
                                                                                
  --reverse       display DNS reverse lookup                                    
  
  --reversevalid  only display results having a valid reverse dns, except if
                  ports specified                                               
  
  --geo           display geoip (free maxmind)                                  
  
  --banner        display banner                                                
 
  --bannerlen     set banner length grabing
                  default 512

  --bannerraw     display raw banner (as a JSON Buffer)                         
  
  --progress      display progress indicator each seconds
                                                                                
  --status        ports status wanted in results (example --status=OT)
                  T(timeout)
                  R(refused)
                  O(open, default)
                  U(unreachable)
                                                                                
  --scan          scan method
                  tcpconnect (full connect, default)
                  tcpsyn (half opened, not yet implemented)
                  udp (not yet implemented)
                                                                                
  --concurrency   max number of simultaneous socket opened
                  default 500
                                                                                
  --timeout       maximum number of milliseconds before closing the connection
                  default 2000
                                                                                
  --display       display result format (json,xml,console)
                  default console
                                                                                
  --json          shortcut for --display=json                                   
  
  --xml           shortcut for --display=xml                                    
  
  --console       shortcut for --display=console                                
  
  --help          display help                                                  
  
  --about         display about                                                 
  
  --version       display version number                                        
```

Samples output
----------------

* Every ports on localhost, grab banner, display only opened ports 
```
root@debian:~# evilscan 127.0.0.1 --port=0-65535 --banner
127.0.0.1|111||open
127.0.0.1|53||open
127.0.0.1|23|Debian GNU/Linux jessie/sid\r\ndebian login:|open
127.0.0.1|5432||open
127.0.0.1|27017||open
127.0.0.1|28017||open
127.0.0.1|35223||open
127.0.0.1|35491||open
127.0.0.1|39619||open
```

* Every ports on localhost, grab banner, display only opened ports, json output, progress status each seconds
```
root@debian:~# evilscan 127.0.0.1 --port=0-65535 --banner --isopen --istimeout --progress --json
{"_timeStart":"N/A","_timeElapsed":"N/A","_jobsTotal":65535,"_jobsRunning":0,"_jobsDone":0,"_progress":0,"_concurrency":500,"_status":"Starting","_message":"Starting"}
{"_timeStart":1371245901876,"_timeElapsed":1031,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":7638,"_progress":11,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:8138"}
{"_timeStart":1371245901876,"_timeElapsed":2085,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":16137,"_progress":24,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:16637"}
{"ip":"127.0.0.1","port":111,"status":"open"}
{"ip":"127.0.0.1","port":53,"status":"open"}
{"ip":"127.0.0.1","port":23,"banner":"Debian GNU/Linux jessie/sid\\r\\ndebian login:","status":"open"}
{"ip":"127.0.0.1","port":5432,"status":"open"}
{"_timeStart":1371245901876,"_timeElapsed":3107,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":24656,"_progress":37,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:25156"}
{"_timeStart":1371245901876,"_timeElapsed":4166,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":33166,"_progress":50,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:33666"}
{"_timeStart":1371245901876,"_timeElapsed":5215,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":41664,"_progress":63,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:42164"}
{"ip":"127.0.0.1","port":27017,"status":"open"}
{"ip":"127.0.0.1","port":28017,"status":"open"}
{"_timeStart":1371245901876,"_timeElapsed":6217,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":49682,"_progress":75,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:50182"}
{"ip":"127.0.0.1","port":35491,"status":"open"}
{"ip":"127.0.0.1","port":35223,"status":"open"}
{"ip":"127.0.0.1","port":39619,"status":"open"}
{"_timeStart":1371245901876,"_timeElapsed":7234,"_jobsTotal":65535,"_jobsRunning":500,"_jobsDone":57732,"_progress":88,"_concurrency":500,"_status":"Running","_message":"Scanning 127.0.0.1:58232"}
{"_timeStart":1371245901876,"_timeElapsed":8182,"_jobsTotal":65535,"_jobsRunning":0,"_jobsDone":65535,"_progress":100,"_concurrency":500,"_status":"Finished","_message":"Scanning 127.0.0.1:65535"}
```

Tips :
--------
**Conncurrency and fast scan**

By default, concurrency is 100. Which is slow when you are scanning large ip range or large port range. You can pass a bigger value using --concurrency option. 1000 is fast by example.
On some linux, only 1024 opened sockets are allowed in the same time. To break this limit, you have to update ulimit parameter of your linux first :

```
ulimit -u unlimited
```

In all cases, due to #25, you will not be able to scan more than 16580355 ip addr at the moment.


**Pause/unpause**

You can pause/unpause a running scan by sending SIGUSR2 signal. First time it will pause the process, second time it will unpause it.
```
kill -SIGUSR2 19859 # where 19859 is the pid of nodejs process running evilscan
```

Help needed
----------
Do not hesitate to open issues and ask for features. Right now i just need "connect" scan. UDP implementation or syn scans should be great (forks are welcome). More and more tests.

If you are using evilscan, **please star it** !



Thank you
----------
Special thanks to authors of theses nodejs modules :
* [node-portscanner](https://github.com/baalexander/node-portscanner) for the inspiration
* [qjobs](https://github.com/franck34/qjobs "qjobs") pause/unpause queue jobs is really cool

and [dailyjs](http://dailyjs.com/) ;)


