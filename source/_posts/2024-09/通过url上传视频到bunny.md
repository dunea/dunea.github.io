---
title: 通过url把视频上传到bunny stream视频库
date: 2024-09-26 08:27:32
categories:
    - 云服务
tags:
    - 后端
    - Python
    - 云服务
    - 视频上传
    - 视频库
    - 视频库上传
    - 视频库上传视频
    - 视频上传到视频库
    - 视频上传到bunny
    - 视频上传到bunny stream
    - 视频上传到bunny stream视频库
---



在本教程中，我们将介绍如何在 Django 项目中使用 BunnyCDN API 实现视频上传和获取视频信息的功能。

## 1. 安装依赖

首先，确保你已经安装了 `requests` 库：

```bash
pip install requests
```

## 2. 配置 Django 项目

在你的 Django 项目的 `settings.py` 文件中，添加 BunnyCDN 的相关配置：

```python
# settings.py

BUNNY_VIDEO_LIBRARY_ID = 'your_video_library_id'
BUNNY_API_KEY = 'your_api_key'
```

## 3. 实现视频上传功能

在你的 Django 项目中创建一个新的文件 `bunny_utils.py`，并添加以下代码：

```python:bunny_utils.py
import requests
from django.conf import settings
from .exceptions import BadGateway  # 假设你有一个自定义的异常类
from .models import BunnyUploadResult  # 假设你有一个模型类来存储上传结果

def _bunny_use_url_upload(video_url: str) -> BunnyUploadResult:
    url = f"https://video.bunnycdn.com/library/{settings.BUNNY_VIDEO_LIBRARY_ID}/videos/fetch"
    headers = {
        "AccessKey": settings.BUNNY_API_KEY,
        "Content-Type": "application/json",
        "Accept": "application/json",
    }
    data = {"url": video_url}

    try:
        response = requests.post(url=url, headers=headers, json=data)  # 使用 json 参数

        if response.status_code != 200:
            raise BadGateway("提交视频到Bunny失败")

        data_json = response.json()
        if not data_json["success"]:
            raise BadGateway(data_json["message"] or "提交视频到Bunny未成功")

        return BunnyUploadResult(
            id=data_json["id"],
            lib_id=settings.BUNNY_VIDEO_LIBRARY_ID,
        )
    except Exception as e:
        raise BadGateway(
            detail="提交视频到Bunny异常",
            error=str(e),
        )
```

## 4. 实现获取视频信息功能

在同一个文件 `bunny_utils.py` 中，添加以下代码：

```python:bunny_utils.py
import requests
from django.conf import settings
from .exceptions import BadGateway  # 假设你有一个自定义的异常类
from .models import BunnyTranscodeInfo  # 假设你有一个模型类来存储转码信息

def _bunny_stream_transcode_info(video_id: str) -> BunnyTranscodeInfo:
    url = f"https://video.bunnycdn.com/library/{settings.BUNNY_VIDEO_LIBRARY_ID}/videos/{video_id}"
    headers = {
        "AccessKey": settings.BUNNY_API_KEY,
        "Accept": "application/json",
    }

    try:
        response = requests.get(url, headers=headers)

        if response.status_code != 200:
            raise BadGateway("获取Bunny视频信息失败")

        data_json = response.json()

        return BunnyTranscodeInfo(
            id=data_json["guid"],
            lib_id=data_json["videoLibraryId"],
            width=data_json["width"],
            height=data_json["height"],
            size=data_json["storageSize"],
            duration=data_json["length"],
            transcode=True if data_json["status"] == 4 else False,
            is_public=data_json["isPublic"],
        )
    except Exception as e:
        raise BadGateway(
            detail="获取Bunny视频信息异常",
            error=str(e),
        )
```

## 5. 使用示例

在你的视图中调用这些函数：

```python:views.py
from django.http import JsonResponse
from .bunny_utils import _bunny_use_url_upload, _bunny_stream_transcode_info

def upload_video(request):
    video_url = request.POST.get('video_url')
    result = _bunny_use_url_upload(video_url)
    return JsonResponse({'id': result.id, 'lib_id': result.lib_id})

def get_video_info(request, video_id):
    info = _bunny_stream_transcode_info(video_id)
    return JsonResponse({
        'id': info.id,
        'lib_id': info.lib_id,
        'width': info.width,
        'height': info.height,
        'size': info.size,
        'duration': info.duration,
        'transcode': info.transcode,
        'is_public': info.is_public,
    })
```

## 6. 总结

通过以上步骤，我们在 Django 项目中实现了使用 BunnyCDN API 上传视频和获取视频信息的功能。希望这篇文章能帮助你更好地集成 BunnyCDN。如果你有任何问题或建议，欢迎在评论区留言。