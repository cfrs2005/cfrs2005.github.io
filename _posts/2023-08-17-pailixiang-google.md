---
title: Enhancing Photo Recognition for My Son's Military Summer Camp using Google Photos
date: 2023-08-17 08:00:00 +0800
categories: [Technology, Image Recognition]
tags: [Google Photos, Image Recognition, Military Summer Camp, Photo Identification]
img_path: '/assets/img/202308/'
image:
 path: 20231120_son_military_camp.png
 alt: Explore how I improved photo recognition for my son's military summer camp images using Google Photos.
---

My eldest son participated in this year's summer military camp, and the photo streaming service we used was PixLive. However, PixLive's image recognition capabilities fell short, failing to accurately identify my son's photos.

I wanted to use Google Photos' recognition to precisely determine which photos featured my eldest son. As a result, I developed the following code for this purpose:

## 一、获取相册所有的照片数据

```
curl -o a.log 'https://www.pailixiang.com/Portal/Services/AlbumDetail.ashx?t=2&rid=req33mip594yecb' \
-H 'authority: www.pailixiang.com' \
-H 'accept: application/json, text/javascript, */*; q=0.01' \
-H 'accept-language: zh-CN,zh;q=0.9' \
-H 'content-type: application/x-www-form-urlencoded; charset=UTF-8' \
-H 'cookie: COOKIE_012=1787ba89-d4a1-4400-a22b-7c5bb89a2d8d; Plx_OA6K05m8=m0zk4q2p1hyjb1qczvs2xtye; SERVERID=3cb4a6b6e759b320e9e7834796a8c5b0|1691152725|1691151948' \
-H 'origin: https://www.pailixiang.com' \
-H 'referer: https://www.pailixiang.com/album_ia4423717371.html' \
-H 'sec-ch-ua: "Not/A)Brand";v="99", "Google Chrome";v="115", "Chromium";v="115"' \
-H 'sec-ch-ua-mobile: ?0' \
-H 'sec-ch-ua-platform: "macOS"' \
-H 'sec-fetch-dest: empty' \
-H 'sec-fetch-mode: cors' \
-H 'sec-fetch-site: same-origin' \
-H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36' \
-H 'x-requested-with: XMLHttpRequest' \
--data-raw 'albumId=adf4416e-3147-476e-9b23-c4671caf5247&groupId=&len=11000&from=&order=0&accessType=1&nw=' \
--compressed
```

## 二、基于Json数据获取对应的图片数据

```shell
cat a.log| grep pbig | awk -F '"' '{print $4}' > file.log
```

## 三、多线程Python下载照片

```python

import requests
import hashlib
import os
from concurrent.futures import ThreadPoolExecutor

url = "https://img.pailixiang.com/album/a343717371/133517343.jpg@!pbig"

# 下载并保存图片
def download_image(url):
response = requests.get(url)
image_data = response.content

    hash_value = hashlib.sha256(url.encode('utf-8')).hexdigest()
    prefix = hash_value[:2]

    directory_num = int(prefix, 16) % 10
    directory = os.path.join(os.getcwd(), str(directory_num))
    os.makedirs(directory, exist_ok=True)

    filename = hash_value + ".jpg"
    filepath = os.path.join(directory, filename)
    with open(filepath, "wb") as f:
        f.write(image_data)

    print(f"Image downloaded successfully: {url}")

def pool_down_images(image_urls):
with ThreadPoolExecutor(max_workers=32) as executor:
executor.map(download_image, image_urls)

def read_file(filename):
with open(filename, 'r') as file:
data = file.readlines()
data = [line.strip() for line in data]

    return data

# 指定文件名并读取数据
filename = "file.log"  # 替换为你的文件名
data = read_file(filename)

# 调用函数上传图片
pool_down_images(data)%
```

## 四、Python服务上传谷歌相册
```python

import requests
from google_auth_oauthlib.flow import InstalledAppFlow

def get_access_token():
# 客户端凭证信息
client_id = ""
client_secret = ""

    # 定义 OAuth 2.0 授权范围（API 的访问权限）
    scopes = ["https://www.googleapis.com/auth/photoslibrary"]

    # 创建授权流程对象
    flow = InstalledAppFlow.from_client_secrets_file(
        "credentials.json",  # 替换为你的客户端凭证文件路径
        scopes=scopes
    )

    # 进行授权
    credentials = flow.run_local_server()

    # 返回访问令牌
    return credentials.token

# 下载并上传图片到谷歌相册
def upload_images(image_urls):
access_token = get_access_token()

    headers = {
        "Authorization": f"Bearer {access_token}"
    }

    for url in image_urls:
        # 下载图片
        response = requests.get(url)
        if response.status_code == 200:
            image_data = response.content

            # 上传图片
            upload_url = "https://photoslibrary.googleapis.com/v1/uploads"
            upload_response = requests.post(upload_url, data=image_data, headers=headers)

            if upload_response.status_code == 200:
                upload_token = upload_response.text.rstrip("\n")
                create_media_item(upload_token, headers)
                print(f"Image uploaded successfully: {url}")
            else:
                print(f"Image upload failed: {url} ({upload_response.text})")
        else:
            print(f"Image download failed: {url} ({response.status_code})")

# 创建相册项
def create_media_item(upload_token, headers):
create_url = "https://photoslibrary.googleapis.com/v1/mediaItems:batchCreate"
body = {
"newMediaItems": [{
"description": "Uploaded from Python",
"simpleMediaItem": {
"uploadToken": upload_token
}
}]
}
response = requests.post(create_url, json=body, headers=headers)

    if response.status_code != 200:
        print(f"Failed to create media item: {response.text}")

def read_file(filename):
with open(filename, 'r') as file:
data = file.readlines()
data = [line.strip() for line in data]

    return data

# 指定文件名并读取数据
filename = "file.log"  # 替换为你的文件名
data = read_file(filename)

# 调用函数上传图片
upload_images(data)
```
