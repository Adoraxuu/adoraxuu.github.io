---
layout: post
title: 'Django : 從零開始學 4 — URLS 路徑'
date: 2024-04-25 20:53:34
author: Adora Xu
tags:
- [Django]
- [Python]
- [URL]

categories:
- [Django]

---

## 資料夾

這是目前my_project內的資料夾結構：

```python
.
└── project
    ├── blog
    │   ├── __pycache__
    │   ├── migrations
    │   │   └── __pycache__
    │   └── templates
    │       └── blog
    └── project
        └── __pycache__
```

## ROOT_URLCONF

可以在`settings.py` 看到我們將`ROOT_URLCONF`設定在`project.urls`

```python
# project/project/settings.py
ROOT_URLCONF = 'project.urls'
```

也就代表Django在使用者拜訪路徑時會先去找`project.urls`（也像Rails的`routes.rb`）可以把`ROOT_URLCONF`想像成指路人，所有APP的路徑都要聽這個檔案指揮

接著我們開啟指路人：`project.urls`

```python
from django.contrib import admin 
from django.urls import path, include #修改這行

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')), #新增這行
]
```

這樣代表，在網址最後加上`/blog/`的時候，他會去找`blog/urls.py`去要資料。

### blog/urls.py（URLconf）

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.list, name='list'),
]
```

這樣代表在網址`/blog/`後面沒接任何東西時，會去找`view`的`list`方法

### project/blog/views.py

```python
from django.shortcuts import render

def list(request):
    return render(request, 'blog/list.html')
```

這樣代表在lsit方法裡，他會發一個request請求，去render 同一個資料夾內 `/templates/blog/list.html` 檔案，如果原本沒有資料夾／檔案就在這裡新增吧～

最後就可以看到成果：

<img src="/images/django-urls/01.png">

Template我只有寫了一行hello world (*°∀°)，下次來使用路徑轉換器&裝飾Template吧～

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Django doc - Urls](https://docs.djangoproject.com/en/5.0/topics/http/urls/)

>[Udemy - Django 2 Web開發入門與實踐](https://www.udemy.com/course/django-2-web/)
