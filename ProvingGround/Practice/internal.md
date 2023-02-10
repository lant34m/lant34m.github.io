> title: ProvingGround_Practice-internal

machine info: This is a 10 point Windows machine. Offensive Difficulty is Easy, same as Community Rated Difficulty.

## Information Gathering

First of all, I use masscan scanning the open ports.

```
masscan -p1-65535,U:53,69,161 $target_ip --rate=5000 -e tun0 -oG masscan-$target_ip
```

![image-20230209225632369](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209225632369.png)

The output file shows me some ports, I use nmap for further infomation.

```
port_str=`cat masscan-$target_ip | grep "/open/tcp/" |cut -f 4 -d ":" |cut -f 2 -d " " |cut -f 1 -d "/" | tr '\n' ',' |sed 's/.$//'`;nmap -A -p$port_str $target_ip -oN nmap-$target_ip
```

![image-20230209231001106](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209231001106.png)

## Initial Access

The Windows version is pretty old, so try nmap with smb scripts.

```
nmap --script smb-vuln*.nse -p 139,445 $target_ip -Pn -oN smb-vuln-scan
```

![image-20230209231400523](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209231400523.png)

The same vuln helpdesk. Easily use msf to exploit.
