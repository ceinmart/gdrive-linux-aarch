# gdrive-linux-aarch

## Overview

This is my own compilation of the gdrive for linux (https://github.com/glotlabs/gdrive) on my tvbox to backup my Home Assistant where run on Docker.
Was very easy process.

I'm sharing here the executable and how to do.  
I don't know if this version will ran over RaspBerry's and similars...  

## my Environment

- TV Box KIPRO Meecool , AMLOGIC s905d : ARMv8 Processor rev 4 (v8l)
- Debian GNU/Linux 11 (bullseye) , which was compiled with [Armbian 23.05.0](https://www.armbian.com/)
- I ran my [Home Assistant](https://www.home-assistant.io/) (docker installation) on this box. 

## What I did

- Installed rust , using this site as reference: https://linux.how2shout.com/how-to-install-and-use-rust-on-debian-11-bullseye/  
  Basically, the command : `curl https://sh.rustup.rs -sSf | sh`
- Downloaded the source of gdrive version 3.9.0 
- Looked on the gdrive github how to compile it, since I never used rust before.
  Found at *Action* section the YAML of the build process.  
  There they have command `cargo build --release` , which ran like a charm.   
  Just run it, where they will download few packages and compile everything, at the end the magic is done, gdrive binary! :)  
```
# file gdrive
gdrive: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=2fcae80aa9403dca946118395076d07a3ff1ab7d, for GNU/Linux 3.7.0, with debug_info, not stripped
```

All this process consumed something of 3GB on my disk, so be awere if you trying to compile it.  
If you have no space enought, my suggestion is plug a USB SSD , redirect the RUST folders to it and then compile.

## Tested? 

Yes!!  
Work's perfectly on my environment.  
Able to connect to my GDrive and manage my files thru command line.  

## TIPS

During the process the gdrive will ask for your ID and Key, will redirect you to copy a link and open at the browser.  
This link will open the google authorizations for the app you've created.  
However they need to give an answer to *gdrive* command which start to listen th eport 8085 and the browser link will answer the authorization thru the `localhost:8085`.  
I was lazzy to start my graphical environment on my box and preffer to use the `iptables` to redirect the port 8085 to localhost . 
For that I ran the commands bellow, where 192.168.0.235 is the IP of my box: 
```
sysctl -w net.ipv4.conf.all.route_localnet=1
iptables -t nat -A PREROUTING -p tcp --dport 8085 -d 192.168.0.235 -j DNAT --to-destination 127.0.0.1:8085
```
Then at finish the google authorization on my browser, they give an error trying to connect on 127.0.0.1 , I just replace to box IP and finished the process with success.

