---
layout: post
title: 'Django : 從零開始學 6 — 自定義URL & 分頁'
date: 2024-05-09 19:31:33
author: Adora Xu
tags:
- [Django]
- [Python]
- [Templates]

categories:
- [Django]

---

## 自定義url

常常看到很多網址後面會接上年/月/日等等的資訊，在Django也是可以自定義URL的

## urls.py

```python
## project/blog/urls.py
app_name = 'blog'
urlpatterns = [
    path('', views.list, name='list'),
    path('author', views.author),
    path(
        '<int:year>/<int:month>/<int:day>/<str:slug>/',
        views.detail,
        name='detail'
    ),
    path('testing/', views.testing, name='testing'),

]
```

首先先到urls定義路徑的顯示方式，這裡定義為`<int:year>/<int:month>/<int:day>/<str:slug>/`

## model.py

```tsx
from django.urls import reverse

    def get_url(self):
        return reverse(
            'blog:detail',
            args=[
                self.published_date.year,
                self.published_date.month,
                self.published_date.day,
                self.slug
            ]
        )
```

在`urls.py`定義了好路徑，需要將資料傳入資料，此時可以使用`reverse` 將值傳給`urls.py`。

## list.html

```python
#project/blog/templates/blog/list.html

<a href="{{ post.get_url }}">
	{{ post.title }}
</a>
```

這時前台就能夠在文章標題上加上路徑了～

---

## 分頁

## Views.py

```python
#project/blog/views.py
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger

def list(request):
    posts = Post.objects.all()
    paginator = Paginator(posts, 3) #每頁三篇
    page = request.GET.get('page')
    try:
        posts = paginator.page(page) #返回第幾頁
    except PageNotAnInteger:
        posts = paginator.page(1)#傳入不是整數，返回第一頁
    except EmptyPage: #傳入的數太大
        posts = paginator.page(paginator.num_pages) #取最後一頁的紀錄
    return render(
        request,
        'blog/list.html',
        {'posts': posts} #把post返回template
    )

```

## list.html

```python
# project/blog/templates/blog/list.html
<div>
    <span>
        {% if posts.has_previous %}
            <a href="?page{{ posts.previous_page_number }}">Previous</a>
        {% endif %}
    </span>
    <span>
        Page {{ posts.number }} of {{ posts.paginator.num_pages }}.
    </span>
    <span>
        {% if posts.has_next %}
            <a href="?page={{ posts.next_page_number }}">Next</a>
        {% endif %}
    </span>
</div>
```

這時就會出現分頁符號了～
<img src="/images/django-url-page/1.png">

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

[Django Docs - paginator](https://docs.djangoproject.com/en/5.0/ref/paginator/)
