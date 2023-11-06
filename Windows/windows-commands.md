# Windows Commands

- ```ipconfig``` - computer's ip information [not shows mac address and dns server]
- ```ipconfig /all``` - all information about computer's network configuration
- ```ipconfig /all | findstr DNS``` - to filter out the content in ip configuration [like Here, DNS]

## To change your ip address

**First release it**
- ```ipconfig /release``` - releases IP.

**Then renew it** 

- ```ipconfig /renew```  - IP gets renewed for all interfaces , to specify the interface - ```ipconfig /release "Wi-Fi"```

- ```ipconfig /displaydns``` - all the websites it knows about and their ip addresses

- ```ipconfig /displaydns | clip``` - copy the contents from the **"ipconfig /displaydns"** command to clipboard

- ```ipconfig /flushdns``` - deletes the DNS resolver cache [Removes any old stale DNS entries]

- ```nslookup``` - to troubleshoot DNS [for querying the Domain Name System (DNS) to obtain the mapping between domain name and IP address, or other DNS records.] - ```nslookup -type=ptr nextdns.io``` OR ```nslookup -type=txt nextdns.io```

- ```cls``` - to clean the screen

- ```getmac /v``` - to get mac address

- ```powercfg /energy``` - to check if you have any power or energy issues

- ```powercfg /batteryreport``` - to get your battery report
- ```assoc``` - tells which file types are associated with which programs, to change to default program for a file type - ```assoc mp4=VLC.vlc```

## Scanning

- ```chkdsk /f``` - it will go through your disk and checks if there are any errors

- ```chkdsk /r``` - checks physical sector issues [This might require a reboot and take some time]

- ```sfc /scannow``` - system file checker [If there are corrupted files, replace and fix them]

- ```DISM /Online /Cleanup-Image /CheckHealth``` - [QUICK CHECKUP] (Deployment Image Servicing and Management) fixes system image

- ```DISM /Online /Cleanup-Image /ScanHealth``` - [DEEP CHECKUP] Takes longer time

- ```DISM /Online /Cleanup-Image /RestoreHealth``` - Runs a scan for corruption and repairs problems that it finds with the operating system you are logged into.


**If somebody plugs a infected USB on your computer, do this -**
- ```tasklist | findstr script``` - looks for any scripts running in the background

- ```taskkill /f /pid 20184``` - kills the task with pid[Process ID] 20184


**(netsh - older brother of ipconfig)**
- ```netsh wlan show wlanreport``` - gives a report on your wireless situation

- ```netsh interface show interface``` - shows all interfaces

- ```netsh interface ip show address | findstr "IP Address"``` - shows all IP Addresses

- ```netsh interface ip show dnsservers``` - shows all dnsservers

## Windows Firewall 
- ```netsh advfirewall set allprofiles state off``` - to turn off windows firewall 

- ```netsh advfirewall set allprofiles state on``` - to turn back on windows firewall

- ```ping "website link"``` - makes sure that servers and websites are up [only for four times]

- ```ping -t "website link"``` - keeps pinging

- ```tracert "website link"``` - traces route to the website

- ```tracert -d "website link"``` - bit faster as not resolves domain name

- ```netstat``` - tells what's conneted to you and what you are connecting to

- ```netstat -af``` - tells which ports are open

- ```netstat -o``` - show process ID (pid) of connections so if anything dangerous is running you can kill it with taskkill

- ```netstat -e -t 5``` - gives sent-receive statistics every five seconds

- ```route print``` - shows the routes your computer will take to get to certain networks 

- ```route add 192.168.40.0 mask 255.255.255.0 10.7.1.44``` - allows us to customize the way our computer reaches certain networks.Above command means we are trying to get to 192.168.40.0 network through 10.7.1.44 gateway

- ```route delete 192.168.40.0``` - deletes this route


- ```shutdown -s``` - it will shutdown your computer

- ```shutdown /r /fw /f /t 0``` - restarts your computer to system BIOS
  
