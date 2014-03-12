---
layout: post
title: "Inspect your android device networking with mitmproxy"
date:   2014-01-08
categories: android
tags: [android, proxy, debug]
comments: true
sharing: true
footer: true
---

### Install and configure mitmproxy


Install and cofigure [Homebrew](http://brew.sh/):

	  ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
	  brew update && brew upgrade 

Install mitmproxy:

	  brew intall mitmproxy
  
The first time mitmproxy or mitmdump is run, a set of certificate files for the mitmproxy Certificate Authority are created in the config directory (~/.mitmproxy by default), so run mitmproxy:
  
	  mitmproxy -p 8888
  
**Note!**
  
When running mitmproxy for the first time, you may encounter an issue:

	  > mitmproxy
	  Error: mitmproxy requires a UTF console environment.
	  Set your LANG enviroment variable to something like en_US.UTF-8
  
    
This is because you might have unset LANG env:
  
	  > locale                                                                                                                                                                                                         
	  LANG=
	  LC_COLLATE="C"
	  LC_CTYPE="C"
	  LC_MESSAGES="C"
	  LC_MONETARY="C"
	  LC_NUMERIC="C"
	  LC_TIME="C"
	  LC_ALL=
    
All you have to do is to add an export to your .zshrc:

	  export LC_ALL=en_US.UTF-8  
	  export LANG=en_US.UTF-8
  
Then source your shell configuration:
  
	  source ~/.zshrc

Push the mitmproxy certificate to the device with adb:

	  adb push ~/.mitmproxy/mitmproxy-ca-cert.cer /sdcard/Downloads
    
On the device, go to the Settings -> Security -> Install from storage -> choose mitmproxy-ca-cert from the dropdown

### Adjust your pc-to-device network configuration (Genymotion)


Turn off your virtual device and navigate to Genymotion -> Settings -> Network -> Check "Use HTTP Proxy"

Check the ip of your device (look for vboxnet* entry):
    
	  > ifconfig  
	  lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
	    options=3<RXCSUM,TXCSUM>
	    inet6 ::1 prefixlen 128
	    inet 127.0.0.1 netmask 0xff000000
	    inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1
	    nd6 options=1<PERFORMNUD>
	  gif0: flags=8010<POINTOPOINT,MULTICAST> mtu 1280
	  stf0: flags=0<> mtu 1280
	  en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	    options=10b<RXCSUM,TXCSUM,VLAN_HWTAGGING,AV>
	    ether 10:dd:b1:ea:aa:57
	    nd6 options=1<PERFORMNUD>
	    media: autoselect (none)
	    status: inactive
	  fw0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 4078
	    lladdr 44:fb:42:ff:fe:88:91:be
	    nd6 options=1<PERFORMNUD>
	    media: autoselect <full-duplex>
	    status: inactive
	  en1: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	    ether a8:bb:cf:0e:1b:e6
	    inet6 fe80::aabb:cfff:fe0e:1be6%en1 prefixlen 64 scopeid 0x6
	    inet 10.70.21.33 netmask 0xffffffc0 broadcast 10.70.21.63
	    nd6 options=1<PERFORMNUD>
	    media: autoselect
	    status: active
	  p2p0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 2304
	    ether 0a:bb:cf:0e:1b:e6
	    media: autoselect
	    status: inactive
	  en3: flags=8822<BROADCAST,SMART,SIMPLEX,MULTICAST> mtu 1500
	    options=60<TSO4,TSO6>
	    ether d2:00:18:89:1b:e0
	    media: autoselect <full-duplex>
	    status: inactive
	  vboxnet0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	    ether 0a:00:27:00:00:00
	    inet 192.168.56.1 netmask 0xffffff00 broadcast 192.168.56.255       
      
In Genymotion's network configuration, set "HTTP Proxy" to 192.168.56.1 (because of "vboxnet0") and "Port" to 8888 (because of "mitmproxy -p 8888") and run the device.

**Note!**

You can verify the network with Genymotion Configuration App at the Homescreen; "IP Management" section.

You should see your http/https traffic in mitmproxy. [Learn](http://mitmproxy.org/doc/mitmproxy.html) to use it! ;)
