---
layout: post
title:  "Raspberry Pi 3 Fix Ethernet Drop"
tags: raspberrypi fix english
---


I don't know exactly what is going on with my Pi, but all of the sudden it drops IP v4 connection and only IP v6 is available.

I don't want to disable IP v6 but I need a way to monitor and fix this. This is basically a reminder for me in case I reinstall Raspbian on my Pi from scratch and forget about this.

<!--more-->

I started to search the internet and found [this post](https://samhobbs.co.uk/2013/11/fix-for-ethernet-connection-drop-on-raspberry-pi) which more or less resembles what I am seeing.

The steps to fix are basically the same, I just modified the script for my use case and BOOM, we are up again! Hope this fix things from now on. I'll keep checking and if I have any problem I'll post an update.

Here is the script:

```sh
#!/bin/bash
LOGFILE=/home/pi/logs/network-monitor.log
LOGFILETMP=/home/pi/logs/network-monitor.tmp

if ip addr show eth0 | grep -q "inet 192.168.1." ;
then
        START_DATE=$(tail -n1 "$LOGFILE" | awk -F' : ' '/Ethernet OK$/ {print $1}')
        [ -z "$START_DATE" ] && START_DATE=$(date +"%m %d %Y %T")
        head -n-1 "$LOGFILE" > "$LOGFILETMP"
        cat "$LOGFILETMP" > "$LOGFILE"
        echo "$START_DATE : $(date "+%m %d %Y %T") : Ethernet OK" >> $LOGFILE
else
        echo "$(date "+%m %d %Y %T") : Ethernet connection down! Attempting reconnection." >> $LOGFILE
        ip link set dev eth0 down
        sleep 3
        ip link set dev eth0 up
        OUT=$? #save exit status of last command to decide what to do next
        if [ $OUT -eq 0 ] ; then
                sleep 3
                STATE=$(ip addr show eth0 | egrep -o "inet 192.168.1.[0-9]{,3}")
                echo "$(date "+%m %d %Y %T") : Network connection reset. Current state is" $STATE >> $LOGFILE
        else
                echo "$(date "+%m %d %Y %T") : Failed to reset ethernet connection" >> $LOGFILE
        fi
fi
```

### Automating with cron

In the post I found the solution uses `/etc/crontab` to automate the checking, but it was not working for me, so I used `sudo crontab -u root -e` to edit the crontab file, then it worked as expected.

### Updates

<b>2018-05-27 20:54 BRT:</b> Updated script to combine all "Ethernet OK" lines into a single line containing two time stamps. (Based on the idea of the original post)