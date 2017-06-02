##ARS-E DAEMON AUTOMATIC REGISTRATION SERVICE EXTENDABLE  

###Running Installations  
[OH2CH - Finland](http://oh2ch.org/trbo/state.php) 

[W8FSM - Fenton, MI](http://ars.moses.bz/) 

###Installation Examples / Demos
[KB9MWR Video](https://youtu.be/gxjyrYZn3Ds)

[W8FSM Rasberry Pi Video](http://youtu.be/j7ItqeQou4k)

[KD8EYF Video](http://youtu.be/85EdiW7mbXQ)  

[KD8EYF Beagle Bone Control Station](http://i.imgur.com/9Uu0T.jpg)  


##2015-03-05 Updates
  Created SMS send function.
  Created NOAA message function.
  By Wodie XE1SWL.

##2013-04-14 Updates
  Created 'updates' branch with following updates. Will test and merge to master. 
  Thanks to anonymous (non-working - still branched) 

* fix tx msg charset encode
* can send group message
* fix gps negative lat lon, maybe work evrywhere now
* allow configure station symbol for every station
* reverse GeoCode address with "Who callsign" command
* send msgs given in files by other program
* less extra channel loading, do set ars_ping_interval to 0

##Install Instructions  
Assumes a Clean install of Debian Squeeze / Wheezy


Update system and install prereqs (as root)
```
apt-get update  
apt-get install build-essential openssh-server git libyaml-tiny-perl libdate-calc-perl libjson-perl  libtest-pod-coverage-perl  
```

Download and install the Ham APRS module
```
mkdir ~/src  
cd ~/src  
wget http://search.cpan.org/CPAN/authors/id/H/HE/HESSU/Ham-APRS-FAP-1.19.tar.gz  
tar -zxvf ./Ham-APRS-FAP-1.19.tar.gz  
cd Ham-APRS-FAP-1.19 
perl Makefile.PL  
make  
sudo make install 
cd ..  
```
Install the TRBO-NET arsed program  

```
git clone https://github.com/KD8EYF/TRBO-NET.git  
cd TRBO-NET/  
perl Makefile.PL  
make  
sudo make install  
```

Install CPAN libraries
Used to access the Google gecoding API:

```
perl -MCPAN -eshell
install Bundle::LWP
```

For Underground weather:

```
sudo cpan Weather::Underground
```

For e-mail sending:

```
sudo cpan -fi Email::Send::SMTP::Gmail
```


Edit the config file by hand to include the DMR radio users you want to listen for.  
the mi5 network config is include as an example of what we did in michigan:  

```
cp configs/arsed.mi5.conf /etc/arsed.conf  
vi /etc/arsed.mi5.conf  
```

Reccommend static networking config in /etc/network/interfaces  
Assuming Radio IP of 192.168.10.1 and PC ip of 192.168.10.
```
    auto-hotplug usb0
    iface usb0 inet static
        address 192.168.10.2
        netmask 255.255.255.0
        up route add -net 12.0.0.0/8 gw 192.168.10.1
        down route del -net 12.0.0.0/8 gw 192.168.10.1
```
RUN THE PROGRAM!

To start automatically at system boot
add 
/usr/local/bin/arsed
to 
/etc/rc.local
```
vi /etc/rc.local
```

```
arsed 
```

Install Apache webserver  
```
apt-get install apache2 libapache2-mod-php5  
cp ~/src/TRBO-NET/web/* /var/www/html
cp ~/src/TRBO-NET/configs/arsed_state.json /var/tmp/
```

connect turbo radio to Linux box using USB  
Device should enumerate and automatically create a network interface. If not check kernel support  


###How To Use
- send text message to gateway radio's ID: it has 'who' command to list registered radios ('w' for short).  
- send 'aprs <callsign> <message>' to send text message to APRS-IS  

once radio starts sending positions, they will be sent to the APRS-IS too  !

View the WebStatus  
- http:/[IP ADDRESS OF SERVER]/state.php  

##Support
Please Feel free to email me, i will be more then happy to help get your installation configured.
