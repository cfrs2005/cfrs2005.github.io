---
title: Website Compromise Analysis Methods and Recommended Scan Tools
date: 2024-05-22 08:00:00 +0800
categories: [Technology,Security]
tags: [Website Security,Hacking,Analysis Tools]
img_path: '/assets/img/202405/'
image:
 path: sqlmap.png
 alt: Explore the methods for analyzing website compromises and discover recommended scan tools in our guide.
---


This article discusses various methods for analyzing website compromises and provides recommendations for simple scan tools to use.



## 起因

通过百度搜索 公司网站后发现，百度的点击跳转都会跳转到色情网站，比如这个链接：

https://www.baidu.com/link?url=vIrZRgyQ4-SnHzlkWwFULrGU8xorAfsM6jnQ_ALLKOemPlPbCQfaIpo0Lg9WpmI1CzXw_I0u3ZShI-BIINofoq&wd=&eqid=f9bb3fcb0038416100000005664c3b6f

## 分析

通过正常URL访问 没有任何问题 ，正常打开，但是通过百度的link 访问就有问题.


```shell
#正常结果请求
curl -s  -w 'HTTP Status: %{http_code}\nLocal IP: %{local_ip}\nRemote IP: %{remote_ip}\n' https://www.festool.com.cn/product/list/cid/1.html 

HTTP Status: 200
Local IP: 192.168.198.12
Remote IP: 116.62.140.98


#异常结果请求

curl -s  -w 'HTTP Status: %{http_code}\nLocal IP: %{local_ip}\nRemote IP: %{remote_ip}\n' https://www.festool.com.cn/product/list/cid/1.html   -H 'referer: https://www.baidu.com/link?url=vIrZRgyQ4-SnHzlkWwFULrGU8xorAfsM6jnQ_ALLKOemPlPbCQfaIpo0Lg9WpmI1CzXw_I0u3ZShI-BIINofoq&wd=&eqid=f9bb3fcb0038416100000005664c3b6f' 

HTTP Status: 200
Local IP: 192.168.198.12
Remote IP: 116.62.140.98
```

得到相关代码

![sqlmapcode.png](sqlmapcode.png)


可见通过百度，360，搜狗的相关跳转会跳转到相关的色情网站。但这里实际上还有个地方需要注意，是网站的Response已经被替换为新的输出，而不是网站里有额外的Js代码来实现的，说明是动态服务的源码被入侵了


## 其他好玩的 [mac可用]

### sqlmap扫描

```

# gitclone code

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

# 增加指令alais

alias sqlmap="python3 /Users/aaa/sqlmap/sqlmap-dev/sqlmap.py"

# 注入检测
sqlmap -u "https://www.festool.com.cn/product/view?id=576730" --batch --level=5 --risk=3 -p id --dbs --random-agent --threads=10
```

![sqlmap.png](sqlmap.png)
