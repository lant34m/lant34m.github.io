> title: ProvingGround_Practice-wallla

machine info: This is a 20 point Linux machine. Offensive Difficulty is Easy, same as Community Rated Difficulty.

## Information Gathering

First of all, I use masscan scanning the open ports.

```
masscan -p1-65535,U:53,69,161 $target_ip --rate=5000 -e tun0 -oG masscan-$target_ip
```

![image-20230210005153164](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210005153164.png)

I try the nmap command as usual, the result shows me all ports are filtered. So it obviously a firewall in target machine. Make some little change.

```
port_str=`cat masscan-$target_ip | grep "/open/tcp/" |cut -f 4 -d ":" |cut -f 2 -d " " |cut -f 1 -d "/" | tr '\n' ',' |sed 's/.$//'`;nmap -p$port_str -sC -sS -sV -Pn  $target_ip -oN nmap-$target_ip
```

![image-20230210011202606](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210011202606.png)

## Initial Access

Honestly, that's a tough one. I have tried each ports on nmap result, but I can't find any vulns. So I search the walkthrough, and finally locate my fault.

There is a Web service on 8091 port. Visit the web.

`On port 8091, I was presented with an HTTP basic auth login prompt.`That's also what I did.

![image-20230210123122278](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210123122278.png)

I actually saw a walkthrough with the capture down here:

![image-20230210123255910](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210123255910.png)

I tried everything but didn't work.

But `Going back to the nmap search results, I see “Basic realm=RaspAP” in the results.` help alot. This information indicates us, this authorize use the http-basic. You can find basic realm in http request.

![image-20230210124703152](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210124703152.png)

And google it, I find a official website and github page.

![image-20230210124221240](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210124221240.png)

Both page can find the credentials.

![image-20230210124333183](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210124333183.png)

![image-20230210124913272](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210124913272.png)

Easily find the version number on about page.

![image-20230210125007948](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210125007948.png)

Find a exploit script on google. [CVE-2020-24572]([CVE-2020-24572-POC/exploit.py at main · gerbsec/CVE-2020-24572-POC (github.com)](https://github.com/gerbsec/CVE-2020-24572-POC/blob/main/exploit.py))

![image-20230210125100551](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210125100551.png)

reverse shell successfully.

![image-20230210125548822](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210125548822.png)

![image-20230210125611752](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210125611752.png)

The result of `sudo -l ` can find a python with sudo priv.

![image-20230210125844379](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210125844379.png)

Remove the origin file and create a new reverse python script.

![image-20230210152912416](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210152912416.png)

That's correct!

![image-20230210153002429](https://raw.githubusercontent.com/lant34m/pic/main/img/image-20230210153002429.png)
