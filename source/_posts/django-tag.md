---
layout: post
title: 'Django: 從零開始學8 - 使用django-taggit 增加標籤'
date: 2024-05-26 23:00:32
author: Adora Xu
tags:
- [Django]
- [Python]
- [form]
- [taggit]
- [tag]

categories:
- [Django]

---

## 安裝Django-taggit

`poetry add django-taggit`

## 到Blog.model裡引入&使用

```bash
from django.db import models

from taggit.managers import TaggableManager

class Post(models.Model):
    # ... 其他程式
    tags = TaggableManager()
```

執行 `poetry run python manage.py makemigrations blog`

會出現以下結果：

<img src="/images/django-tag/0.png">

執行`poetry run python manage.py migrate`

可以看到增加了兩張表：

<img src="/images/django-tag/1.png">


## 使用ORM新增tag

### 進到shell操作看看

`python manage.py shell`

`from blog.models import Post`

```bash
>>> post = Post.objects.all().first()
>>> post.tags
<taggit.managers._TaggableManager object at 0x1049a3ef0>
>>> post.tags.add('python', 'django')
```

到資料庫裡面就能看到剛剛新增的tag

<img src="/images/django-tag/2.png">


另外一個`taggit_taggeditem`則是紀錄了關聯，到shell也可以查到剛剛增加的tag：

```bash
>>> post.tags.all()
<QuerySet [<Tag: python>, <Tag: django>]>
```

如果想要刪除則可以使用`post.tags.remove(’python’)` 刪除(僅刪掉關聯)

## 將tag加到頁面

```bash
#project/blog/templates/blog/list.html
<p>
	Tags {{ post.tags.all | join:', ' }}
</p>
```

就可以看到tags了：

<img src="/images/django-tag/3.png">


也可以再加上判斷句，有tags才顯示

```bash
{% if post.tags.count %}
	<p>
		Tags {{ post.tags.all | join:', ' }}
	</p>
{% endif %}
```

<img src="/images/django-tag/4.png">

## 增加標籤連結

```bash
#project/blog/urls.py
from django.urls import path
from . import views

app_name = 'blog'
urlpatterns = [
# ...其他連結設定
# 新增tag連結
    path('tag/<str:tag_slug>/',
         views.list, name='posts_by_tag'),

]
```

## views新增過濾

```bash
#project/blog/views.py
from taggit.models import Tag

def list(request, tag_slug=None):
    if tag_slug:
        tag = Tag.objects.filter(slug=tag_slug).first()
        posts = Post.objects.filter(tags__in=[tag]) #只要包含我們搜尋的tag就列出來
    else:
        posts = Post.objects.all() #原本放在外面，要放到else，指如果沒有tag_slug參數就顯示所有文章
#....其他
```

修改網址加上tag/python，就會出現依照tag過濾的文章列表：

<img src="/images/django-tag/5.png">

## 前台畫面加上連結

```bash
#project/blog/templates/blog/list.html

#...其他程式
        {% if post.tags.count %}
        <p>
            Tags:
            {% for tag in post.tags.all %}
            <a href="{% url "blog:posts_by_tag" tag.slug %}">
                {{ tag.name }}
            </a>
            {% if not forloop.last %}, {% endif %}
            {% endfor %}
        </p>
        {% endif %}
#...其他程式
```

- `<a href="{% url "blog:posts_by_tag" tag.slug %}">`
    - 連結可以使用`{% url “app_name:path_name” 參數 %}`
    - 參考project/blog/urls.py 寫的設定，`app_name:path_name = blog:posts_by_tag`

前台就可以看到tag有連結可以點擊：

<img src="/images/django-tag/6.png">

點擊後就可以過濾顯示的文章（也就是轉換連結到/tag/tag.slug）：

<img src="/images/django-tag/7.png">

## 做Tag list的清單

```bash
#project/blog/views.py
def list(request, tag_slug=None):
# 其他程式...
    tag_list = Tag.objects.all()
# 其他程式...
    return render(
        request,
        'blog/list.html',
        {'posts': posts, 'tag_list': tag_list} #把tag_list傳遞給Template
    )
```

```bash
#project/blog/templates/blog/list.html    
    <hr>
    <div>
        <ul>
            {% for tag in tag_list %}
            <li>
                <a href="{% url "blog:posts_by_tag" tag.slug %}">{{ tag.name }}</a>
                ({{ tag.taggit_taggeditem_items.count }}) #顯示 taggit_taggeditem_items table內的數量
            </li>
            {% endfor %}
        </ul>
    </div>
```

前台就會長這樣：

<img src="/images/django-tag/8.png">


本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

- [django-taggit](https://django-taggit.readthedocs.io/en/latest/#)

> [Django 2 Web开发入门与实战](https://www.udemy.com/course/django-2-web/)
