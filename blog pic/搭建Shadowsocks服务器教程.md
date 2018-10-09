搭建Shadowsocks服务器教程

[toc]

### 一、准备

---

#### 选择海外vps服务商

服务商有很多，这里提供两个选择。

##### 1. Vultr

数据中心：日本、洛杉矶、英国、法国、德国、荷兰、澳大利亚等15个机房

套餐价格：KVM 512MB 20GB SSD 500GB月流量 $2.5/月

简单介绍：Vultr作为全球最大的游戏主机提供商背景之一，上线之后以高质的性价比、15个数据中心，以及新注册账户赠送5美金的账户使用金优惠促销，吸引广大的用户。作为我们用户，日本、洛杉矶等数据中心速度较好，如果有需要海外其他机房也可以在其12个数据中心中选择到适合自己的。

官方网站：http://www.vultr.com/ （目前新支持支付宝付款方式）


备注说明：目前VULTR取消注册赠送余额，商家限制较为严格，一个支付方式只能开一个账户。

##### 2. DigitalOcean

数据中心：旧金山、纽约、伦敦、法兰克福、新加坡等8个机房

套餐价格：CPU1核、内存512MB、硬盘20G、流量1000G，5美元/月

简单介绍：DigitalOcean有点类似Vultr商家，云主机产品，按照小时付款，可选8个机房，速度上可能对于中文用户不是太好，但是比如多机房用户，以及海外项目用户，性价比还算可以。

官方网站：http://www.DigitalOcean.com/ （点击邀请链接注册就可以获得新账户$10的奖励充值，可以免费使用2个月VPS主机。但不支持支付宝。）

> PS：商家不会永久稳定，购VPS建议月付且定期备份


#### 购买服务器，注册vps账号

这里以DigitalOcean做示范。

##### 1. 点击邀请链接注册

点击 [邀请链接](https://m.do.co/c/3514649acac1) 后，输入你的邮箱密码，创建账号。

![image](http://ollx3753j.bkt.clouddn.com/%E6%B3%A8%E5%86%8Cdo.png)

##### 2. 验证邮箱，激活账号

![image](http://ollx3753j.bkt.clouddn.com/dodo%E9%AA%8C%E8%AF%81%E9%82%AE%E7%AE%B1.png)

##### 3. 添加支付方式

这里可以选择绑卡或绑PayPal，暂不支持支付宝

![image](http://ollx3753j.bkt.clouddn.com/dodo%E7%BB%91%E5%8D%A1.png)

绑卡后会受到一笔1美元的扣款，别担心，一会会给回你的。

![image](http://ollx3753j.bkt.clouddn.com/dodo%E7%BB%91%E5%8D%A1%E5%AE%8C%E6%AF%95.png)

到此注册就完毕了，通过 Dashboard -> Billing 可以看到你账户已经有10美元了。

### 二、添加服务器

---

##### 1. 点击 Dashboard 的 `Create Droplet`

##### 2. 选择镜像

这里选择 Docker，Docker 开箱即用，出错率更低。

![image](http://ollx3753j.bkt.clouddn.com/dodo%E9%80%89%E6%8B%A9%E9%95%9C%E5%83%8F.png)

当然你也可以不选择，进去服务器再通过以下命令安装。


```
curl -sS https://get.docker.com/ | sh
```

##### 3. 选择 Size

根据你需求选择服务器的size，一般第一个就够用了。

![image](http://ollx3753j.bkt.clouddn.com/dodo%E9%80%89%E6%8B%A9size.png)

##### 4. 选择节点

旧金山速度不错，如果觉得速度慢，可以换个节点。如果在国内访问，千万不要买欧洲（伦敦、荷兰等）和新加坡节点。试过多次，非常慢。

![image](http://ollx3753j.bkt.clouddn.com/dodo%E9%80%89%E6%8B%A9%E7%BA%BF%E8%B7%AF.png)

##### 5. 输入 Droplet 名称

![image](http://ollx3753j.bkt.clouddn.com/dodo%E5%A1%AB%E5%85%A5%E5%90%8D%E7%A7%B0.png)

##### 6. 其他设置不用管，点击 `create` 创建完成，等待创建完成后，你会收到一封邮件，里面有登录IP和密码。

![image](http://ollx3753j.bkt.clouddn.com/dodo%E5%AE%8C%E6%88%90%E5%88%9B%E5%BB%BA.png)

### 三、安装 Shadowsocks

---

##### 1. 初次登录

打开终端，可以用windows自带的命令行工具，输入


```
ssh root@你服务器的ip地址
```

![image](http://ollx3753j.bkt.clouddn.com/dodo%E5%88%9D%E6%AC%A1%E7%99%BB%E5%BD%95.png)

输入yes，然后输入邮件给你的密码

成功后，会有这样一串信息

![image](http://ollx3753j.bkt.clouddn.com/dodo%E5%88%9D%E6%AC%A1%E7%99%BB%E5%BD%95%E6%88%90%E5%8A%9F%E5%90%8E.png)

修改初始密码，完成

##### 2. 安装 Shadowsocks

只需输入一下命令即可安装

```
docker pull oddrationale/docker-shadowsocks

```
完成后输入


```

docker run -d -p 1984:1984 oddrationale/docker-shadowsocks -s 0.0.0.0 -p 1984 -k password -m aes-256-cfb

```
-d——容器启动后会进入后台
-p（第一个）——指定要映射的端口，使用的格式是hostPort:containerPort，即本地的 1984 端口映射到容器的 1984 端口
-s——服务器IP
-p（第二个）——代理端口(默认1080)
password——你的密码
-m——加密方式


> 如果需要多个账号，再执行上面的命令即可

现在来检查一下 SS 有没有安装成功：


```
docker ps

```

![image](http://ollx3753j.bkt.clouddn.com/dodo%E5%AE%89%E8%A3%85ss%E6%88%90%E5%8A%9F.png)

如果你看到 STATUS 是 up 的话，表示服务器端已经配置成功

### 四、客户端配置
---

##### 下载客户端

windows：https://github.com/shadowsocks/shadowsocks-windows/releases

Mac：https://github.com/shadowsocks/ShadowsocksX-NG/releases

Android：https://github.com/shadowsocks/shadowsocks-android/releases

IOS：国区无法直接安装，请参考 https://crifan.github.io/scientific_network_summary/website/server_client_mode/ss_client/client_ios.html

##### 配置

选择 `服务器 -> 编辑服务器 -> 添加` 输入服务器信息

**地址**：[必填]服务器的IP或域名地址

**端口**：[必填]端口号

**加密方法**：aes-256-cfb

**密码**：[必填]密码

![image](http://ollx3753j.bkt.clouddn.com/dodo%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%BC%96%E8%BE%91%E6%9C%8D%E5%8A%A1%E5%99%A8.png)

输入后点击确定，然后启用系统代理，就大功告成。
