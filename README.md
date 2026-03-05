# 3X-UI 从零搭建教程 (第一期) 
## 为什么 ufw 放行无效？Debian 12 + Xray 小白保姆级教学

视频教程：▶https://youtu.be/mzLR9ebXeNY?si=SXw_vof4COhqo4Aq

本文带你在 3X-UI 面板上完成一次完整部署。
底层基于 Xray，系统推荐 Debian 12。

本教程目标：

小白也能成功部署
理解端口放行的真实原理
避免 “ufw 无效” 的常见坑
## 一、服务器准备

六六云服务器：https://kjxl.cc/666clouds（双ISP，支持tiktok）
全站九折优惠：XL666
年付七折优惠：year30off

LightNode 服务器：https://kjxl.cc/LightNode（按流量计费）

Vultr 服务器：https://kjxl.cc/vultr （按时计费，最低6$/月。）

### 准备一台云服务器：

系统：Debian 12
最低 1G 内存即可
打开 22 端口（SSH）
登录服务器后，先更新系统：

    apt update && apt upgrade -y

等它跑完。

安装 curl (如果没有的话)：

    apt install curl -y

## 二、一键安装 3X-UI
执行官方安装脚本：

    bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)

安装完成后，系统会显示：

- 面板端口
- 用户名
- 密码
访问：

     http://服务器IP:面板端口

如果能打开，说明基础安装成功。

## 三、面板打不开？先别急着用 ufw

很多人这一步会失败。

然后开始执行：

    ufw allow 端口

结果报错：

    ufw: command not found

原因很简单：
系统根本没装 ufw。
很多 Debian VPS 默认没有安装 ufw。

## 四、正确的防火墙排错步骤
以后端口打不开，请按这个顺序排查：

### 1、 先确认端口是否监听

    ss -tulnp

示例：如果要查看某个端口，如 80 端口有没有监听？
输入代码：

    ss -tulnp | grep :80

如果没有看到你的端口：

👉 不是防火墙问题

👉 是程序没启动

### 2、检查系统是否有 ufw

    which ufw

有输出 → 用 ufw 放行
没输出 → 不要再用 ufw

### 3、没有 ufw，用 iptables

    iptables -I INPUT -p tcp --dport 端口 -j ACCEPT

保存规则：

    apt install -y iptables-persistent
    netfilter-persistent save

### 4、最容易被忽略的：云安全组

即使服务器内部放行成功， 如果云服务器控制台的安全组没有放行端口， 外网依然无法访问。

登录云平台后台：

- 打开安全组
- 放行面板端口
- 放行节点端口
- 协议选择 TCP
- 来源 0.0.0.0/0
  
## 五、创建第一个节点（保证成功版）

进入 3X-UI 后台。

创建入站：
- 协议：VLESS
- 传输：TCP
- 安全：无加密

这一期不讲 Reality。原因很简单：
先保证你一定能连上。
当你能稳定连接后，再升级高级玩法。

## 核心总结

端口不通，只可能是三件事：

- 程序没监听
- 本机防火墙拦截
- 云安全组拦截
### 记住这句话：

端口不通 = 监听 + 防火墙 + 安全组

不要一上来就乱装 ufw。

## 下一篇预告

基础部署成功之后，下一步我们会讲：
👉 Reality 稳定高级玩法

包括：

- 为什么不用域名也能运行
- Vision 模式的原理
- 如何降低识别风险
- 常见报错排查
  
如果你已经成功搭建，可以继续阅读第二篇。
