# OpenPortNotifier
[![Build Status](https://travis-ci.org/fnzv/OpenPortNotifier.svg?branch=master)](https://travis-ci.org/fnzv/OpenPortNotifier) <br>
Simple tool to monitor network changes over time and trigger alerts.

## Demo
[![asciicast](https://asciinema.org/a/cUL1ksv8JaNrZvM2PgNUjyuyj.png)](https://asciinema.org/a/cUL1ksv8JaNrZvM2PgNUjyuyj)

## Requirements

- Golang (optional if you compile the sources)
- masscan 
- postfix/smtp relay (optional for want E-mail reports)
- mysql (optional if you want to track changes over time)


### Quickstart

Run the bash script (install.sh) to install all the required dependencies.
WARNING: this script will install automatically postfix,mysql-server and masscan

```bash install.sh```

Downloads the binaries and installs them into your system, after you changed the example configuration (/etc/opn/opn.conf) you can run the scans with the command:

```opn```

### Use cases
- Continuos scanning of your Networks and monitor service exposure (All scans are saved into Mysql and/or sent via email/tg).. "pro-tip" -> run on crontab -> http://crontab.guru/
- Constant Alerting of critical service or when a firewall (could be software or hardware) stops working
- Tracking service/hosts from your VPN/Allowed cidr to know where your services are without running everytime the slow Nmap 

### Integrating OPN with Metabase:
- Install Metabase and connect the OPN database, then you can easily create dashboards to dynamically track hosts on your networks.
- The example below is made in 10 minutes (https://www.metabase.com/ - docker run -d -p 3000:3000 --name metabase metabase/metabase) and connected the db to Metabase + running OPN on crontab so every XYZ hour/minutes i can just check the dashboard and see the email report/telegram notifications.

![](OPN-Metabase.png?raw=true)

### Compile

To compile and install binaries run: 
WARNING: this script will install automatically postfix,mysql-server and masscan

```bash compile.sh```

If you already have Golang installed and know your stuff you can just get the sources from the src/ folder and build the .go files.


### How OPN works
- Reads from /etc/opn/opn.conf configuration, if these values (SMTPhost,MysqlAuth,Telegram) are nulled ("") will be skipped and the feature is omitted
- Scan the Networks using Masscan (because nmap is slow)
- Send output to screen and trigger alerts if ports open are on "CriticalPorts" list
- All the scans are saved into the configured database 
- For large scans set a bigger ulimit (open files limit) and max_connections on mysql or the script will fail

### Notes/Pro tips
- Large scale scans require high ulimit setting ( http://posidev.com/blog/2009/06/04/set-ulimit-parameters-on-ubuntu/ )
- Set high ammount of max connections on MySQL to sustain large scans (max_connections = 10000 on my.cnf or SET GLOBAL)
- Once OPN is installed you can just insert the following cron ```00 0  * * * root opn``` to run the scan every day at 00:00
- Installing an OpenVPN/IPSec client and let the traffic route to your VPN/Allowed Network so you can scan your entire nets and avoid FWs

