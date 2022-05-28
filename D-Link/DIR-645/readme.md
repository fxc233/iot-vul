Vendor of the products:D-Link

Reported by: 　　　WangJincheng && FeiXincheng && ShaLetian from X1cT34m

Affected products:D-Link DIR-645

Vendor Homepage:  https://www.dlink.com/en/consumer

Vendor Advisory:  https://tsd.dlink.com.tw/ddgo


# summarize

`D-Link DIR-645` was discovered to contain a command injection vulnerability when operate the file `__ajax_explorer.sgi`. This vulnerability allows attackers to execute arbitrary commands via the `QUERY_STRING` parameter.

# Vulnerability description

We can see that the os will get `QUERY_STRING` in `scandir_main`, and pass it to `sub_410AD4`

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/1.png)

In `sub_410AD4`, it calls `sub_410434`

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/2.png)

In `sub_410434`, it contains a command injection.

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/3.png)

before the attack

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/3.png)

after the attack

![image-20220316191407126](https://github.com/fxc233/CVE/blob/main/img/3.png)

# poc

	curl "http://192.168.0.1/portal/__ajax_explorer.sgi?action=umnt&path=path&where=here&en=;echo%20X1cT34mpwner%20>FXC;"