TCPA简介
腾讯内部使用的TCPA，由腾讯TEG操作系统组研发，基于RHEL7.4源码，定制化的TCPA。
团队介绍：
腾讯TEG操作系统组, 2010年成立，专业的内核团队,维护研发腾讯内部linux操作系统tlinux, 保证百万级server高效稳定运行， 为腾讯业务提供有力支撑。

优势：TCPA启用后，小文件比BBR能提升40%以上，大文件比BBR能提升5%~10%。TCPA的优势在于小文件的性能提升，程序也默认仅加速网站端口(80/443/8080)，所以更适用于建站场景。

安装要求
系统：centos7以上
boot分区不小于500M(太小可能导致安装内核失败)
一键包安装
wget http://down.08mb.com/tcp_opz/tcpa/tcpa.sh
sh tcpa.sh
1
2
wget http://down.08mb.com/tcp_opz/tcpa/tcpa.sh
sh tcpa.sh
使用说明:一键包会自动安装依赖(仅epel-release、net-tools)和内核并重启，重启后安装自动完成无需人工干预。

手动安装
部署流程：
安装必要依赖:

yum -y install net-tools
1
yum -y install net-tools
更换系统内核

[root@vultr ~]# wget http://down.08mb.com/tcp_opz/tcpa/kernel-3.10.0-693.5.2.tcpa06.tl2.x86_64.rpm
[root@vultr ~]# rpm -ivh kernel-3.10.0-693.5.2.tcpa06.tl2.x86_64.rpm --force
Preparing...                          ################################# [100%]
Updating / installing...
   1:kernel-3.10.0-693.5.2.tcpa06.tl2 ################################# [100%]
Install kernel
Set Grub default to "3.10.0-693.5.2.tcpa06.tl2" Done.
1
2
3
4
5
6
7
[root@vultr ~]# wget http://down.08mb.com/tcp_opz/tcpa/kernel-3.10.0-693.5.2.tcpa06.tl2.x86_64.rpm
[root@vultr ~]# rpm -ivh kernel-3.10.0-693.5.2.tcpa06.tl2.x86_64.rpm --force
Preparing...                          ################################# [100%]
Updating / installing...
   1:kernel-3.10.0-693.5.2.tcpa06.tl2 ################################# [100%]
Install kernel
Set Grub default to "3.10.0-693.5.2.tcpa06.tl2" Done.
重启操作系统

reboot
1
reboot
下载主程序：

wget http://down.08mb.com/tcp_opz/tcpa/tcpa_packets_180619_1151.tar.gz
1
wget http://down.08mb.com/tcp_opz/tcpa/tcpa_packets_180619_1151.tar.gz
开始安装：

tar xf tcpa_packets_180619_1151.tar.gz
cd tcpa_packets
sh install.sh
1
2
3
tar xf tcpa_packets_180619_1151.tar.gz
cd tcpa_packets
sh install.sh
TCPA(默认只加速80,443,8080这3个端口)，如需新增加速端口:

vim /usr/local/storage/tcpav2/start.sh
1
vim /usr/local/storage/tcpav2/start.sh
第46行后添加:

$BINDIR/$CTLAPP access add tip $ip tport 自定义端口
1
$BINDIR/$CTLAPP access add tip $ip tport 自定义端口
启动tcpa拥塞算法：

cd /usr/local/storage/tcpav2
sh start.sh
1
2
cd /usr/local/storage/tcpav2
sh start.sh
查看是否开启成功

[root@vultr tcpav2]# lsmod|grep tcpa
tcpa_engine           224249  0
1
2
[root@vultr tcpav2]# lsmod|grep tcpa
tcpa_engine           224249  0
卸载方法：

cd /usr/local/storage/tcpav2
sh uninstall.sh
1
2
cd /usr/local/storage/tcpav2
sh uninstall.sh
速度测试
1、脚本测试

TCPA：



BBR PLUS：



脚本测速跑起来，看着和BBR PLUS差别不大

2、服务器上放置HTML5 Speedtest测试

TCPA：



BBR PLUS：



下载15.83mbps 上传10.43mbps。比BBR PLUS低了很多。

通过HTML5 speedtest可以看到。腾讯的TCPA带宽利用率并没有BBR plus高。没有BBR PLUS那么暴力！

3、网页打开速度测试

TCPA：



BBR PLUS：



网页整体完成时间是1.9s。这相比BBR PLUS，网页打开速度减少了接近1半！

要知道，我这个网页是在国内的，加上CDN之后，完全打开也要2s左右。

但是在美国迈阿密，使用了TCPA，完全加载完成仅需1.9s.

这相比BBR PLUS，进步了不少！

4、从国外服务器拉文件测试



第一个为124KB的小文件，耗时0.6s下载完成，均速218kB/S

第二个为5.65M的中型文件，耗时4.0s下载完成，均速1.4MB/s

总结：如果你只是用来建站，没有什么大文件，那么建议使用腾讯TCPA，如果是你的是需要大带宽高流量，建议便宜BBR。
