> title: ProvingGround_Practice-clamav

machine info: This is a 10 point Linux machine. Offensive Difficulty is Intermidiate, same as Community Rated Difficulty.

## Information Gathering

First of all, I use masscan scanning the open ports.

```
masscan -p1-65535,U:53,69,161 $target_ip --rate=5000 -e tun0 -oG masscan-$target_ip
```

![image-20230209235327810](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209235327810.png)

The output file shows me some ports, I use nmap for further infomation.

```
port_str=`cat masscan-$target_ip | grep "/open/tcp/" |cut -f 4 -d ":" |cut -f 2 -d " " |cut -f 1 -d "/" | tr '\n' ',' |sed 's/.$//'`;nmap -A -p$port_str $target_ip -oN nmap-$target_ip
```

![image-20230209235339276](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209235339276.png)

## Initial Access

There is no vulns I can find by searchsploit for ssh and smb. By I actually find some exploit for sendmail with clamav.

![image-20230209235552171](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209235552171.png)

I copy those two file to my folder. Find a CVE in file. And other looks like the exploit will be opening a new port 31337 and running sh as root.

try run perl script first.

![image-20230210000145306](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210000145306.png)

It works.

![image-20230210000242281](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210000242281.png)

The CVE exploit is same as above. [go script]([[GO\] ClamAV Milter Remote Code Execution [CVE-2007-4560] (github.com)](https://gist.github.com/0xjbb/fdf1678addf0c957bf2b284b29e4dff4))
