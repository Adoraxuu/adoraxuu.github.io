---
layout: post
date: 2024-04-24 19:54:22
title: 'Django : 從零開始學 3 — ORM'
author: Adora Xu
tags:
- [Django]
- [Python]
- [ORM]

categories:
- [Django]

---

## Django shell

在終端機執行以下指令會進入互動模式(像是Rails的`$rails c`指令)：

*`$ python manage.py shell`*

<img src="/images/django-orm/1.png">


## 載入Models

首先會需要將建立的models import

```python
>>> from blog.models import Post
>>> from django.contrib.auth.models import User
```

## get()

可以先看一個簡單的範例：

```python
>>> user = User.objects.get(username='admin')
>>> user
```

使用get()有些注意事項，像是僅能返回「唯一」的條件，如果有多個符合則會報錯，如果 `get()` 發現多個對象，會引發一個 [Model.MultipleObjectsReturned](https://docs.djangoproject.com/zh-hans/5.0/ref/models/class/#django.db.models.Model.MultipleObjectsReturned) 異常：

```python
Entry.objects.get(name="A Duplicated Name")  # raises Entry.MultipleObjectsReturned
```

這個比較像是Ruby on Rails的`find()`，不過`find()`的參數為要查找的對象的 ID(這裡則可以選擇)，沒找到返還`ActiveRecord::RecordNotFound` 例外訊息。

另外一個也蠻像的，就是Rails的`find_by()` 則會返回符合條件的第一筆資料，沒找到返回`nil`（順便複習Rails？）。

## filter()

返回一个新的 [QuerySet](https://docs.djangoproject.com/zh-hans/5.0/ref/models/querysets/#django.db.models.query.QuerySet)，包含查詢的參數對象，如果沒找到會返回一個空的QuerySet

```python
>>> post = Post.objects.filter(title='hi')
>>> post
<QuerySet [<Post: hi>]>
```

這就比較像Rails的`where()`，找到會返回 `ActiveRecord::Relation` 包含一個或多個與參數符合的對象，如果沒找到：會返回一個空的  `ActiveRecord::Relation`。

### filter()找不到的情況

```python
>>> post = Post.objects.filter(title='aaa')
>>> post
<QuerySet []>
```

### exclude()

排除查找的參數

```python
>>> post = Post.objects.exclude(title='hi')
>>> post
<QuerySet [<Post: created from shell>]>
```

## all()

秀出所有東西：

```python
>>> posts = Post.objects.all()
>>> posts
<QuerySet [<Post: second-post>, <Post: hi>]>
```

可以使用for迴圈將文章標題印出來：

```python
>>> for post in posts:
...     print(post.title)
... 
second-post
hi
```

### 新增

```python
>>> post = Post(title='created from shell', slug='create-from-shell', body='this is body', author=user, status= 'draft')
>>> post
<Post: created from shell>
>>> post.save()
>>> posts
<QuerySet [<Post: second-post>, <Post: hi>]>
```

這裡需要`.save()` 才會將資料儲存到資料庫裡

## 更新

```python
>>> post.title
'created from shell'
>>> post.title = 'new created from shell'
>>> post
<Post: new created from shell>
```

更新倒是蠻容易的，只要用個`=` 即可

## first()

第一個對象

```python
>>> post = Post.objects.all().first()
>>> post
<Post: created from shell>
```

## last()

最後一個對象

```python
>>> post = Post.objects.all().last()
>>> post
<Post: hi>
>>> posts = Post.objects.all()
>>> posts
<QuerySet [<Post: created from shell>, <Post: hi>]>
```

## order_by()

```python
>>> post = Post.objects.order_by('title')
>>> post
<QuerySet [<Post: created from shell>, <Post: hi>, <Post: second-post>]>
>>> post = Post.objects.order_by('-title')
>>> post
<QuerySet [<Post: second-post>, <Post: hi>, <Post: created from shell>]>
```

可以用`-`去調整正序或是倒序，這時返回的會是QuerySet

也可以使用索引`[0]`，來返回指定的值：

```python
>>> post = Post.objects.filter(title='hi')[0]
>>> post
<Post: hi>
```

這時返回的就是單一值

## delete()

```python
>>> post = post.filter(title='second-post')
>>> post.delete()
(1, {'blog.Post': 1})
>>> Post.objects.all()
<QuerySet [<Post: created from shell>, <Post: hi>]>
```

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Django doc - Making quries](https://docs.djangoproject.com/en/5.0/topics/db/queries/)

>[Django doc - QuerySet](https://docs.djangoproject.com/zh-hans/5.0/ref/models/querysets/#get)

>[Udemy - Django 2 Web開發入門與實踐](https://www.udemy.com/course/django-2-web/)

