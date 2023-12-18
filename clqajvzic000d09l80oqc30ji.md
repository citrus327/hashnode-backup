---
title: "Wsl2 中使用网络代理配置"
datePublished: Wed Jan 25 2023 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajvzic000d09l80oqc30ji
slug: wsl2
tags: wsl

---

## 背景

最近电脑升级了 windows11 且卸载了 ubuntu 系统，家里开发环境就顺带移步到 WSL2。之前就听说 wsl2 的整体网络设计不同于 wsl1 非常麻烦，一直在逃避搞代理这回事儿。现在由于 npm publish 速度实在堪忧，设置代理这关是逃不掉了，前后折腾了 2 个小时总算搞明白了。在此记录。

## 过程

1. 关闭自动更新 dns nameserver
    

```bash
#/etc/wsl.conf
[network]
generateResolvConf = false
```

1. 添加以下脚本至`.bashrc`或`.zshrc`
    

```bash
# add for proxy
export hostip=$(ip route | grep default | awk '{print $3}')
export hostport=1011#根据代理IP自行填入
alias proxy='
    export HTTPS_PROXY="<http://$>{hostip}:${hostport}";
    export HTTP_PROXY="<http://$>{hostip}:${hostport}";
    export ALL_PROXY="<http://$>{hostip}:${hostport}";
    echo -e "Acquire::http::Proxy \\"<http://$>{hostip}:${hostport}\\";" | sudo tee -a /etc/apt/apt.conf.d/proxy.conf > /dev/null;
    echo -e "Acquire::https::Proxy \\"<http://$>{hostip}:${hostport}\\";" | sudo tee -a /etc/apt/apt.conf.d/proxy.conf > /dev/null;
'
alias unproxy='
    unset HTTPS_PROXY;
    unset HTTP_PROXY;
    unset ALL_PROXY;
    sudo sed -i -e '/Acquire::http::Proxy/d' /etc/apt/apt.conf.d/proxy.conf;
    sudo sed -i -e '/Acquire::https::Proxy/d' /etc/apt/apt.conf.d/proxy.conf;
```

1. 执行`proxy`设置代理
    
2. 每次重启 wsl 后, /etc/resolv.conf 中的内容会被重置，所以需要执行以下内容：
    

```bash
#<https://github.com/microsoft/WSL/issues/5420#issuecomment-646479747>
sudo rm /etc/resolv.conf
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
sudo bash -c 'echo "[network]" > /etc/wsl.conf'
sudo bash -c 'echo "generateResolvConf = false" >> /etc/wsl.conf'
sudo chattr +i /etc/resolv.conf
```

1. 为代理软件设置防火墙放行 win11 下通过`防火墙和网络保护` - `允许应用通过防火墙`，将 v2ray（根据个人情况而定，我用得是 v2ray）设置为放行
    
2. ![](https://user-images.githubusercontent.com/17166940/210820027-e460b728-486c-406c-afdb-4736c6be1a62.png align="left")
    
    ![](https://user-images.githubusercontent.com/17166940/210820147-5853c0d6-8959-4dff-8727-0768a3c2a5b1.png align="left")
    
    v2ray 设置允许局域网访问
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1702881817235/8952fcfe-cd40-42e0-8138-5a9f93e96c3e.png align="center")
    
3. 使用 curl 测试是否成功
    

```bash
curl -vv google.com
```

## References

* [https://github.com/microsoft/WSL/issues/5420](https://user-images.githubusercontent.com/17166940/210820027-e460b728-486c-406c-afdb-4736c6be1a62.png)
    
* [https://zhuanlan.zhihu.com/p/414627975](https://user-images.githubusercontent.com/17166940/210820027-e460b728-486c-406c-afdb-4736c6be1a62.png)
    
* [https://github.com/2dust/v2rayN/issues/1573](https://user-images.githubusercontent.com/17166940/210820027-e460b728-486c-406c-afdb-4736c6be1a62.png)
    
* [https://jiayaoo3o.github.io/2020/06/23/记录一次WSL2的网络代理配置/](https://user-images.githubusercontent.com/17166940/210820027-e460b728-486c-406c-afdb-4736c6be1a62.png)
    

## Troubleshooting

1. 占位程序接收到错误数据 - 管理员权限下执行`netsh winsock reset` 重启电脑解决。