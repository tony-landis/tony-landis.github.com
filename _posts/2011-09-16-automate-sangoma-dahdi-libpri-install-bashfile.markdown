---
layout: posts
categories: bash sangoma dahdi libpri debian
title: Automate dahdi, libpri, Sangoma Wanpipe install on Debian Squeeze
---

Automate dahdi, libpri, Sangoma Wanpipe install on Debian Squeeze
===

Sangoma has a helpful [install page for wanpipe/dahdi on Linux](http://wiki.sangoma.com/wanpipe-linux-asterisk-dahdi).

This bask script will make things much simpler. I have tested it on Debian Squeeze 
but it should work on other releases as well. 

{% highlight bash %}
wget https://raw.github.com/gist/1223525/ae8f9e4553d7420ef4225c468f73ddfd365b0732/sangoma.sh | bash -
{% endhighlight %}

Here is the full script. Feel free to change the version numbers or clone (the gist)[https://gist.github.com/1223525].

{% highlight bash %}
#!/bin/bash

DA='dahdi-linux-complete-2.5.0.1+2.5.0.1'
WAN="wanpipe-3.5.23"
PRI="libpri-1.4.12"

apt-get -y install build-essential
apt-get -y install gcc
apt-get -y install g++
apt-get -y install automake
apt-get -y install autoconf
apt-get -y install libtool
apt-get -y install make
apt-get -y install libncurses5-dev
apt-get -y install flex
apt-get -y install bison
apt-get -y install patch
apt-get -y install libtool
apt-get -y install linux-source-$(uname -r)
apt-get -y install linux-headers-$(uname -r)
apt-get -y install libsctp-dev
apt-get -y install libtool

cd /usr/src 

## GET FILES
wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/releases/${DA}.tar.gz
wget http://downloads.asterisk.org/pub/telephony/libpri/releases/${PRI}.tar.gz
wget ftp://ftp.sangoma.com/linux/current_wanpipe/${WAN}.tgz

# DECOMPRESS FILES
tar -zxvf ${DA}.tar.gz 
tar -zxvf ${PRI}.tar.gz 
tar xvfz ${PRI}.tgz 

# COMPILE DAHDI
cd /usr/src/${DA}
make all && make install & make config
cd ..

# COMPILE LIBPRI
cd /usr/src/${PRI}
make && make install
cd ..

# COMPILE WANPIPE
cd -${WAN}
make dahdi DAHDI_DIR=/usr/src/${DA}/linux
make install

# CONFIG WANPIPE
wancfg_dahdi

# CHECK
wanrouter hwprobe
wanrouter stop
wanrouter start
/etc/init.d/asterisk stop
/etc/init.d/asterisk start

exit 0

{% endhighlight %}

