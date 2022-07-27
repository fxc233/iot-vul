# Information

Vendor of the products:WAVLINK

Reported by: FeiXincheng(FXC030618@outlook.com) && WangJincheng(wjcwinmt@outlook.com)  && ShaLetian(ltsha@njupt.edu.cn) from X1cT34m

Affected products:WAVLINK  WL-WN575A3

Affected firmware version: RPT75A3.V4300.201217

Vendor Homepage:  https://www.wavlink.com/en_us

Vendor Advisory:  https://www.wavlink.com/en_us/firmware/details/fac744bd61.html

# Summarize

`WAVLINK  WL-WN575A3` was discovered to contain a command injection vulnerability when operate the file `adm.cgi`. This vulnerability allows attackers to execute arbitrary commands via the `username` parameter.

# Show the product

`Wavlink WL-WN575A3` s a AC1200 Dual-band Wi-Fi Range Extender. The test version here is `RPT75A3.V4300.201217`

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/1.png)

# Vulnerability details

The vulnerability is detected at `/etc_ro/lighttpd/www/cgi-bin/adm.cgi`

At first, from the `_start`entry enters, and then the `ftext` function is executed.

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/2.png)

In the function `ftext`, we find that we can controll the content of `page` field is `sysinit`, we can execute the `set_sys_init` function.

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/3.png)

In the function `set_sys_init`, the program uses function `web_get` to obtain the content of parameter  `username` , `newpass`  which are sent by `POST` request. Then, when `newpass!= 0`, the content username is formatted into a string passed as an argument to the function `do_system` which can execute system commands.

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/4.png)

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/5.png)



# poc

Send the following to the URL `http://wifi.wavlink.com/cgi-bin/adm.cgi` by `POST` request.

```
page=sysinit&username=fxc`ls>/etc_ro/lighttpd/www/fxc.html`
```

#### Before attack

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/6.png)

#### After attack

![image-20220316191407126](https://github.com/fxc233/iot-vul/blob/main/WAVLINK/WN575A3/img/7.png)
