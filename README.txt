Configuration Guide:

Location of the files: 

/etc/dnsmasq.conf
/etc/ntpsec/ntp/ntp.conf

Step 1: Scroll down until you see this section

_____________________________________________________________________________________________________________
# listen address is the raspberry pi IP address
listen-address=10.131.18.253,127.0.0.1
# A record for High Availability, if just using standalone use the below one, just remove the hash
# host-record=hostname.domain.local,10.131.18.200
# Use yor own IP scheme
address=/demo.barco.local/10.131.18.200
address=/demo.barco.local/10.131.18.201
address=/demo.barco.local/10.131.18.202
#SRV record
srv-host=_barcomanagement._tcp.barco.local,demo.barco.local,8883

# DHCP options
dhcp-range=10.131.18.105,10.131.18.220
dhcp-option=option:router,10.131.18.1
dhcp-option=option:ntp-server,10.131.18.253
dhcp-option=6,10.131.18.253
# Server IP addresses Reservation
# If you are running a cluster use all three reservations. If not you can comment it out by using #
dhcp-host=00:00:00:00:00:00,10.131.18.200,SAS01
dhcp-host=00:00:00:00:00:00,10.131.18.201,SAS02
dhcp-host=00:00:00:00:00:00,10.131.18.202,SAS03

# Domain name
domain=barco.local
______________________________________________________________________________________________________________

------------------------------------------------------------------------------------------------

Step 2: Edit the file as you need. Remember to change the SRV record along with the domain name and host address. They need to match like below.

SRV:
- srv-host=_barcomanagement._tcp.barco.local,demo.barco.local,8883

A record:
- host-record=hostname.domain.local,10.131.18.200

Domain name
- domain=barco.local

-------------------------------------------------------------------------------------------------

Step 3: Go to the location of ntp.conf or simply use the command below to edit:

sudo nano  /etc/ntpsec/ntp.conf

Make sure it looks like this:
__________________________________________________________________________________________________________

# /etc/ntpsec/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntpsec/ntp.drift
leapfile /usr/share/zoneinfo/leap-seconds.list

# To enable Network Time Security support as a server, obtain a certificate
# (e.g. with Let's Encrypt), configure the paths below, and uncomment:
# nts cert CERT_FILE
# nts key KEY_FILE
# nts enable

# You must create /var/log/ntpsec (owned by ntpsec:ntpsec) to enable logging.
#statsdir /var/log/ntpsec/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

# This should be maxclock 7, but the pool entries count towards maxclock.
tos maxclock 11

# Comment this out if you have a refclock and want it to be able to discipline
# the clock by itself (e.g. if the system is not connected to the network).


# Specify one or more NTP servers.
server 127.127.1.0 prefer
fudge 127.127.1.0 stratum 6

# Public NTP servers supporting Network Time Security:
# server time.cloudflare.com nts

# pool.ntp.org maps to about 1000 low-stratum NTP servers.  Your server will
# pick a different set every time it starts up.  Please consider joining the
# pool: <https://www.pool.ntp.org/join.html>
# pool 0.debian.pool.ntp.org iburst <------------ Add a hash at the beginning to disable
# pool 1.debian.pool.ntp.org iburst <------------ Add a hash at the beginning to disable
# pool 2.debian.pool.ntp.org iburst <------------ Add a hash at the beginning to disable
# pool 3.debian.pool.ntp.org iburst <------------ Add a hash at the beginning to disable

# Access control configuration; see /usr/share/doc/ntpsec-doc/html/accopt.html
# for details.
#
# Note that "restrict" applies to both servers and clients, so a configuration
# that might be intended to block requests from certain clients could also end
# up blocking replies from your own upstream servers.
restrict 10.131.18.0 mask 255.255.255.0 nomodify notrap
# By default, exchange time with everybody, but don't allow configuration.
restrict default kod nomodify nopeer noquery limited

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1
_______________________________________________________________________________________________________________

Also make sure to delete or add a hash for tos minclock 4 minsane 3, otherwise NTP will not work.




