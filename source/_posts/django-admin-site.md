---
layout: post
title: 'Django: 從零開始學 2 - admin site'
date: 2024-04-23 18:54:35
author: Adora Xu

tags:
- [Django]
- [Python]
- [MTV]
- [admin]
- [admin site]

categories:
- [Django]
---
## admin站點使用

Django有內建Admin Site，讓開發者可以輕鬆管理資料。
原本新建專案的時候還在疑惑，為什麼DB裡面已經有這麼多Table，原來是給內建的admin使用的。

要怎麼進去admin呢？
執行`$ python manage.py runserver`時，在網址最後面輸入`/admin`就能夠看到登入介面：

<img src="/images/django-admin-site/0.png">

## 創建帳號密碼

在終端機輸入就可以設定admin的帳號密碼：
`$ python manage.py createsuperuser`

## 登入帳號密碼

回到剛剛的 `/admin` 就可以看到預設的Table了

<img src="/images/django-admin-site/1.png">

咦，那昨天做的POST呢？需要手動加入

## 把POST加進去admin裡

```python
# project/blog/admin.py

from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    pass

```

這時就能看到POST

<img src="/images/django-admin-site/2.png">

點擊Posts，可以看到文章列表

<img src="/images/django-admin-site/3.png">

可以按右上角的ADD POST增加資料

<img src="/images/django-admin-site/4.png">

## 自定義admin顯示資料

Django也提供了很多自定義

```python
# project/blog/admin.py

from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ('title', 'slug','author', 'published_date') # 顯示欄位
    list_filter = ('author', 'published_date') # 按照作者 or 發佈日期篩選文章
    search_fields = ('title', 'body') # 搜尋
    prepopulated_fields = {'slug': ('title',)} # 自動填寫：輸入title時自動填寫slug

```

關於更多客製化，可以參考：[django docs - admin](https://docs.djangoproject.com/en/5.0/ref/contrib/admin/)

這時看我們的POST就更豐富了：

<img src="/images/django-admin-site/5.png">
也可以手動新增資料

<img src="/images/django-admin-site/6.png">

記得在學習Ruby on Rails的時候，對於ORM和資料庫都不熟悉，操作起來不免有點礙手礙腳，Djang內建Admin站點蠻貼心的，一開始安裝就自動內建了٩(^ᴗ^)۶

不過Django的ORM使用方法也是需要熟悉，就留到下篇來說明！
本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料

[Django docs - admin](https://docs.djangoproject.com/zh-hans/5.0/ref/contrib/admin/)

[MDN - Admin_site](https://developer.mozilla.org/zh-TW/docs/Learn/Server-side/Django/Admin_site)

[Udemy - Django 2 Web開發入門與實踐](https://www.udemy.com/course/django-2-web/)

