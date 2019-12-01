$ sudo tcpdump -i eth1 -l -e -n | ./netbps
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 96 bytes
11:36:53    2143.33 Bps
11:37:03    1995.99 Bps
11:37:13    2008.35 Bps
11:37:23    1999.97 Bps
11:37:33    2083.32 Bps
131 packets captured
131 packets received by filter
0 packets dropped by kernel
#!/usr/bin/perl
use strict;
use warnings;
use Time::HiRes;

my $reporting_interval = 10.0; # seconds
my $bytes_this_interval = 0;
my $start_time = [Time::HiRes::gettimeofday()];

STDOUT->autoflush(1);

while (<>) {
  if (/ length (\d+):/) {
    $bytes_this_interval += $1;
    my $elapsed_seconds = Time::HiRes::tv_interval($start_time);
    if ($elapsed_seconds > $reporting_interval) {
       my $bps = $bytes_this_interval / $elapsed_seconds;
       printf "%02d:%02d:%02d %10.2f Bps\n", (localtime())[2,1,0],$bps;
       $start_time = [Time::HiRes::gettimeofday()];
       $bytes_this_interval = 0;
    }
  }
}
#!/bin/bash

INTERVAL="1"  # update interval in seconds

if [ -z "$1" ]; then
        echo
        echo usage: $0 [network-interface]
        echo
        echo e.g. $0 eth0
        echo
        echo shows packets-per-second
        exit
fi

IF=$1

while true
do
        R1=`cat /sys/class/net/$1/statistics/rx_packets`
        T1=`cat /sys/class/net/$1/statistics/tx_packets`
        sleep $INTERVAL
        R2=`cat /sys/class/net/$1/statistics/rx_packets`
        T2=`cat /sys/class/net/$1/statistics/tx_packets`
        TXPPS=`expr $T2 - $T1`
        RXPPS=`expr $R2 - $R1`
        echo "TX $1: $TXPPS pkts/s RX $1: $RXPPS pkts/s"
        #!/bin/bash

INTERVAL="1"  # update interval in seconds

if [ -z "$1" ]; then
        echo
        echo usage: $0 [network-interface]
        echo
        echo e.g. $0 eth0
        echo
        exit
fi

IF=$1

while true
do
        R1=`cat /sys/class/net/$1/statistics/rx_bytes`
        T1=`cat /sys/class/net/$1/statistics/tx_bytes`
        sleep $INTERVAL
        R2=`cat /sys/class/net/$1/statistics/rx_bytes`
        T2=`cat /sys/class/net/$1/statistics/tx_bytes`
        TBPS=`expr $T2 - $T1`
        RBPS=`expr $R2 - $R1`
        TKBPS=`expr $TBPS / 1024`
        RKBPS=`expr $RBPS / 1024`
        echo "TX $1: $TKBPS kB/s RX $1: $RKBPS kB/s"
