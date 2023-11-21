---
title: VPS FOR SingBOX Notes
date: 2023-11-20 08:00:00
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [VPS,TRACE,SINGBOX]   
img_path: '/assets/img/202311/'
---


## install singbox

```
# update yum download somethings

yum update && yum -y install curl wget tar socat git openssl util-linux gcc-c++ zlib-devel openssl-devel libevent-devel bind-utils cronie


# install jq

yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install jq

#install singbox

wget -N -O /root/singbox.sh https://raw.githubusercontent.com/TinrLin/sing-box/main/Install.sh && chmod +x /root/singbox.sh && ln -sf /root/singbox.sh /usr/local/bin/singbox && bash /root/singbox.sh


```


## install testtrace

```

wget https://raw.githubusercontent.com/nanqinlang-script/testrace/master/testrace.sh 


```
