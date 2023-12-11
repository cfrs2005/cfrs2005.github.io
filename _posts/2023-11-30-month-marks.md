---
title: Miscellaneous Content Collection within the Month
date: 2023-11-30 08:00:00 +0800
categories: [ Technology, Miscellaneous ]
tags: [ Twitter Video Download, Elasticsearch, Kubernetes Quick Start ]
img_path: '/assets/img/202311/'
image:
  path: 20231130twitter.png
  alt: Explore various tech topics including Twitter video downloading, Elasticsearch, and a quick intro to Kubernetes within our collection.
---

This is a compilation of miscellaneous content from the past month, covering topics such as downloading Twitter videos,
Elasticsearch, and a quick start guide to Kubernetes.

## Twitter Download with Ios shortcut

1. [dwonload](https://dl.fastwave.tw/twitter/update)

## k8s Lens download without

## k8s lesson

1. [k8s lesson](https://juejin.cn/user/2963939081856328/posts)

## Chrome Delete Site Data

```shell
chrome://settings/content/all?searchSubpage=openai
```

## 死磕ES

1. [慢查询](https://mp.weixin.qq.com/s/RTpBaFpNELQCO6VE0KMfsw)
2. [慎用WildCard](https://mp.weixin.qq.com/s/JO0YM-t5EdDgzOP_0_r6Ow)
3. [Ngram分词配置](https://mp.weixin.qq.com/s?__biz=MzI2NDY1MTA3OQ==&mid=2247484758&idx=1&sn=1fa663c5f8b85a82ef25f8453af88394&chksm=eaa82d7edddfa4682a2ff4de9465d8d6fc99a6f925c3f018c0e77b6ee8f59406c687854648cf&scene=21#wechat_redirect)

## ES MAC ARM 安装

目前官方不支持 arm

```shell
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.10.2
```

## Kibana docker 启动

```shell
docker run --name my-kibana -d -p 5601:5601 docker.elastic.co/kibana/kibana:7.15.0
```

## 检测海外翻墙IP

```shell
curl 'http://sspanel.net/ip.php' \
  -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7' \
  -H 'Accept-Language: zh-CN,zh;q=0.9' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Pragma: no-cache' \
  -H 'Referer: http://www.ip111.cn/' \
  -H 'Upgrade-Insecure-Requests: 1' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36' \
  --compressed \
  --insecure
```
