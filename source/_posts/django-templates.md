---
layout: post
date: 2024-04-26 18:47:41
title: 'Django : 從零開始學 5 — Templates'
author: Adora Xu
tags:
- [Django]
- [Python]
- [Templates]

categories:
- [Django]

---

之前做完urls，今天就可以來處理前台顯示畫面Templates啦 ʕ •̀ o •́ ʔ

## blog/views

```python
from django.shortcuts import render
from .models import Post

def list(request):
    posts = Post.objects.all()
    return render(
        request,
        'blog/list.html',
        {'posts': posts}
    )
```

先改寫blog/vies裡面的lsit，讓list頁面能有我們建好model的post

## project/blog/templates/blog/list.html

```python
{% block content %}
    <h1>My blog</h1>
    {% for post in posts %}
        <h2>
            <a href="{{ post.get_url }}">
                {{ post.title }}
            </a>
        </h2>
        <p>
            Published {{ post.published_date|date:"SHORT_DATE_FORMAT" }} by {{ post.author }}
        </p>
        {{ post.body|truncatewords:30|linebreaks }}
    {% endfor %}
{% endblock %}
```

- **變數：{{ variable }}**
- 過濾器：舉幾個比較常見的例子
    - default:
        - `{{ value|default:"nothing" }}`
        - 如果變數為 false 或空，則使用給定的預設值。否則，使用變數的值。例如：
    - **[length](https://docs.djangoproject.com/en/5.0/ref/templates/builtins/#std-templatefilter-length):**
        - `{{ value|length }}`
        - 傳回值的長度。這適用於字串和列表。例如：
    - date:
        - `{{ value|date:"SHORT_DATE_FORMAT" }}`
        - 可自定義像是：
        - `{{ value|date:"D d M Y" }} {{ value|time:"H:i" }}`
    - 可以參考：[Django Docs - 內建過濾參考](https://docs.djangoproject.com/en/5.0/ref/templates/builtins/#ref-templates-builtins-filters)
- 標籤：
    - for:`{% for post in posts %}….. {% endfor %}`
    - if..elif..else
    - if 可以和過濾器一起使用
    
    ```python
    {% if athlete_list|length > 1 %}
       Team: {% for athlete in athlete_list %} ... {% endfor %}
    {% else %}
       Athlete: {{ athlete_list.0.name }}
    {% endif %}
    ```
    

這時候前面看起來效果就會像：

<img src="/images/django-templates/1.png">

## 模板繼承

模板繼承有點像是Rails的render，先把可以替換的地方挖空，變成base模板，子模板就可以自定義（覆蓋）挖空地方的內容

### Base模板

首先看我們的base模板

```python
# template.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome, Templates Page</title>
    <style>
        header {
            background-color: #333;
            color: white;
            padding: 20px;
            text-align: center;
        }
    </style>
</head>
<body>

<header>
    <h2>Welcome, Templates Page</h2>
</header>

<div class="content">
    {% block mymessage %}
    {% endblock %}
</div>

</body>
</html>
```

其實上面內容多半都是一般的html，只有一個地方比較特別

```python
<div class="content">
    {% block mymessage %}
    {% endblock %}
</div>
```

這裡的    `{% block mymessage %} {% endblock %}` 可以被子模板替換

### 子模板

```python
{% extends 'template.html' %}

{% block mymessage %}
  <p>hihihi</p>
{% endblock %} 
```

最上面加上 `{% extends 'template.html' %}` 就可以把 `block mymessage`換掉

<img src="/images/django-templates/2.png">

其實就蠻像Rails的Render / Component ٩(●ᴗ●)۶

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Django Docs - templates](https://docs.djangoproject.com/en/5.0/ref/templates/)

