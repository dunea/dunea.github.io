---
title: 通过url把远程图片上传到cloudflare image图片库
date: 2024-09-26 08:27:49
categories:
    - 云服务
tags:
    - 后端
    - Python
    - 云服务
    - Cloudflare
    - Cloudflare Image
---






在本教程中，我们将介绍如何在 Django 项目中使用 Cloudflare Image API 实现图片上传功能。

<!-- more -->

## 1. 安装依赖

首先，确保你已经安装了 `requests` 库：

```bash
pip install requests
```


## 2. 配置 Django 项目

在你的 Django 项目的 `settings.py` 文件中，添加 Cloudflare 的相关配置：

```python
# settings.py

CF_ACCOUNT_ID = 'your_cloudflare_account_id'
CF_API_TOKEN = 'your_cloudflare_api_token'
```


## 3. 创建上传图片功能

在你的 Django 项目中创建一个新的文件 `cloudflare_utils.py`，并添加以下代码：

```python
import requests
from django.conf import settings
from .exceptions import BadGateway  # 假设你有一个自定义的异常类
from .models import CloudflareImageUploadResult  # 假设你有一个模型类来存储上传结果

def _cf_image_use_url_upload(image_url: str) -> CloudflareImageUploadResult:
    url = f"https://api.cloudflare.com/client/v4/accounts/{settings.CF_ACCOUNT_ID}/images/v1"
    headers = {
        "Authorization": f"Bearer {settings.CF_API_TOKEN}",
    }
    files = {
        "url": (None, image_url),
        "metadata": (None, "{}"),
        "requireSignedURLs": (None, "false"),
    }

    try:
        response = requests.post(url, files=files, headers=headers)

        if response.status_code != 200:
            raise BadGateway("提交图片到CloudflareImage失败")

        data_json = response.json()

        if not data_json["success"]:
            raise BadGateway("提交图片到CloudflareImage未成功")

        return CloudflareImageUploadResult(
            image_id=data_json["result"]["id"],
            require_signed=data_json["result"]["requireSignedURLs"],
        )
    except Exception as e:
        raise BadGateway(
            detail="提交图片到CloudflareImage异常",
            error=str(e),
        )
```


## 4. 创建模型类

在你的 Django 项目中创建一个新的文件 `models.py`，并添加以下代码：

```python
from django.db import models

class CloudflareImageUploadResult(models.Model):
    image_id = models.CharField(max_length=255)
    require_signed = models.BooleanField()
```


## 5. 创建自定义异常类

在你的 Django 项目中创建一个新的文件 `exceptions.py`，并添加以下代码：

```python
from rest_framework.exceptions import APIException

class BadGateway(APIException):
    status_code = 502
    default_detail = 'Bad Gateway'
    default_code = 'bad_gateway'
```


## 6. 使用示例

在你的视图中调用这些函数：

```python
from django.http import JsonResponse
from .cloudflare_utils import _cf_image_use_url_upload

def upload_image(request):
    image_url = request.POST.get('image_url')
    result = _cf_image_use_url_upload(image_url)
    return JsonResponse({'image_id': result.image_id, 'require_signed': result.require_signed})
```


## 7. 总结

通过以上步骤，我们在 Django 项目中实现了使用 Cloudflare Image API 上传图片的功能。希望这篇文章能帮助你更好地集成 Cloudflare Image。如果你有任何问题或建议，欢迎在评论区留言。