---
title: 'Django: 從零開始學 1 - 設計Table'
layout: post
author: Adora Xu
tags:
- [Django]
- [Python]
- [MTV]

categories:
- [Django]
---
<img src="/images/django-01/banner.png">

> 圖片來源[Usplash - Brecht Corbeel](https://unsplash.com/@brechtcorbeel)

## 前言

學了Ruby on Rails之後，想說再繼續學其他的框架和語言，那就決定是最熱門的Python語言所以使用的網頁框架Django了 ٩(^ᴗ^)۶，本系列會是我以初階網頁工程師的角度所寫的學習筆記，歡迎不吝指教～

## 開啟新專案

因為Django不像Rails有內建的套件管理工具，所以我使用Poetry做為套件管理工具，詳細下載教學可以參考：[https://python-poetry.org/docs/](https://python-poetry.org/docs/)

- 新增資料夾`$ mkdir my_project`
- 使用vscode開啟資料夾
- 新增 pyproject.toml  `$ poetry init --no-interaction --dependency django`
- 安裝依賴項目 `$ poetry install`
- 進入虛擬環境 `$ poetry shell`
- 初始化專案 `$ poetry run django-admin startproject project`
- 進入專案資料夾 `$ cd project`
- 跑資料庫遷移 `$ python manage.py migrate`
- 開啟伺服器 `$ python manage.py runserver`

### MVC v.s. MTV?

在Django不是採用MVC架構，而是採用MTV，差別如下：

| MTV | MVC |
| --- | --- |
| Model | Model |
| Template | View |
| View | Controller |

關於MVC，可以參考之前所寫的文章：[Ruby on Rails: MVC是什麼?](https://adora-xu.com/2023/12/02/MVC/)

## APP?

Django將整個網站分成很多模塊，可以把不同模塊整理出來，稱為app，
像是如果要製作blog app，可以運用指令生成：
`$ python manage.py startapp blog`

運行指令後會長出很多資料夾：

<img src="/images/django-01/01.png">

這些檔案的相關說明：

| 檔案 | 說明 |
| --- | --- |
| migrations | 資料庫遷移資料 |
| admin.py | 內建的admin站點 |
| models.py | 資料庫設計 |
| test.py | 測試檔案 |
| view.py | MVC 的 controller|

看字面意思應該也不難猜裡面的內容～

## 設計Table

- 每個model都是一個 Python class，繼承自 [django.db.models.Model](https://docs.djangoproject.com/en/5.0/ref/models/instances/#django.db.models.Model)
- 模型的每個屬性代表一個資料庫欄位。

**[Model fields](https://docs.djangoproject.com/en/1.8/ref/models/fields/)** 可為 Django Model 定義不同型態的屬性，比較常見的有：

| 類型 | 說明 |
| --- | --- |
| CharField | 字串欄位，適合像 title、location 這種有長度限制的字串。 |
| TextField | 適合放大量文字的欄位 |
| URLField | URL 設計的欄位 |
| DateTimeField | 日期與時間的欄位，使用時會轉成 Python datetime 型別。 |

當然不只以上這些屬性，還有更多屬性可以參考：[Django docs](https://docs.djangoproject.com/en/5.0/topics/db/models/)

理解基本觀念之後，就可以來設計我們的Table了～

```python
# project/blog/models.py
from django.db import models # 導入Django.db的model
from django.utils import timezone # 導入Django時區工具
from django.contrib.auth.models import User #導入Django默認的User Model

class Post(models.Model): # 定義一個Post的model class，繼承自(models.Model)

    STATS_OPTIONS = ( 
        ('draft', 'Draft'), 
        ('published', 'Published'), 
    )
    
    title = models.CharField(max_length=200)
    author = models.ForeignKey(
        User,
        on_delete=models.CASCADE,
        related_name='blog_posts', 
        )
    
    body = models.TextField()
    published_date = models.DateTimeField(default=timezone.now)
    created_date = models.DateTimeField(auto_now_add=True)
    updated_date = models.DateTimeField(auto_now=True)
    status = models.CharField(
        max_length=10,
        choices=STATS_OPTIONS,
        default='draft')
    
    class Meta:
        ordering = ('-published_date',)

    def __str__(self):
        return self.title
```

上面內容所代表的意思：
<img src="/images/django-01/03.png">

數據庫設計好後，要和Django說，我們有一個blog的app，需要在數據庫進行初始化：

```python
# project/project/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog.apps.BlogConfig', # 添加這行
]
```
添加完成之後就可以運行 `$ python manage.py makemigrations blog`
生成我們的Table啦～

輸入完成後會出現以下文字：

> blog/migrations/0001_initial.py
    - Create model Post
> 


blog/migrations資料夾生成`0001_initial.py`文件，這個檔案到底是什麼東西呢？(´･ω･`)  ？ 又做了什麼事呢？

想知道的話，可以透過以下命令，查看這裡執行的SQL語法，這樣就更一目了然了٩(๑❛ᴗ❛๑)۶：

`$ python manage.py sqlmigrate blog 0001`

```sql
BEGIN;
--
-- Create model Post
--
CREATE TABLE "blog_post" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "title" varchar(200) NOT NULL, "body" text NOT NULL, "published_date" datetime NOT NULL, "created_date" datetime NOT NULL, "updated_date" datetime NOT NULL, "status" varchar(10) NOT NULL, "author_id" integer NOT NULL REFERENCES "auth_user" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "blog_post_author_id_dd7a8485" ON "blog_post" ("author_id");
COMMIT;
```

可以從指令中看出，我們新增了一張名稱為`blog_post`的Table，另外也新建了我們在上面所填寫的欄位，而預設欄位都是`NOT NULL`。

Django和Rails一樣都使用ORM，我們就不用特地使用SQL語法來操作資料庫了～

最後可以執行`$ python manage.py migrate`
就完成了Table的建立了！

## 添加新欄位

做到後面發現忘記加slug，要怎麼增加呢？
一樣到建立Model的`blog/models.py` ，在剛剛新增的`Class Post`內新增：

```python
#my_project/project/blog/models.py
slug = models.SlugField(max_length=200, unique=True, blank=True)
```

接著運行 `$ python manage.py makemigrations blog`    

最後運行`$ python manage.py migrate`

就完成了新增～沒想到光新增Table就寫了一大篇(´･ω･`)，剩下的就留到下一次分享了～
本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料：

>[Django docs](https://docs.djangoproject.com/en/5.0/topics/db/models/)

> [Django Girl]([https://djangogirlstaipei.gitbooks.io/django-girls-taipei-tutorial/content/django/models.html](https://djangogirlstaipei.gitbooks.io/django-girls-taipei-tutorial/content/django/models.html))

> [Udemy - Django 2 Web開發入門與實踐]([https://www.udemy.com/course/django-2-web/learn/lecture/14381524#overview](https://www.udemy.com/course/django-2-web/))
