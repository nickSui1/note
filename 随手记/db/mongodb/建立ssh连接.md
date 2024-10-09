### 建立ssh连接
 - 在linux系统中，进入/etc/sysconfig/network-scripts目录<br>
`cd /etc/sysconfig/network-scripts`<br>
 - 使用vim编辑器打开网络配置文件<br>
`vi ifcfg-enp0s3`<br>
添加以下配置:<br>

BOOTPROTO="static"<br>
IPADDR="这里填写要设置的ip地址"<br>
NETMASK="255.255.255.0"<br>
GATEWAY="192.168.99.1" 本地网关<br>
DNS1="114.114.114.114" DNS服务器 <br>

然后将ONBOOT配置修改为yes，保存退出后，重启网络服务:<br>
`service network restart`<br>

- mac终端terminal自带ssh功能<br>
  - 在终端输入 ssh -p 端口号 服务器名称@ip<br>
 `ssh -p 22 nicksui@192.168.3.15`