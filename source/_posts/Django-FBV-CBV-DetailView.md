---
layout: post
title: 'Django: FBV & CBV — DetailView'
date: 2024-10-08 00:00:00
tags: [Django, FBV, CBV, DetailView]
categories: Django-CBV
---

<img src="/images/Django-FBV-CBV-DetailView/detail_view.webp">

## FBV

假設你我們要建立一個部落格，而其中部落格的文章的詳細頁面，使用 FBV 可以這樣子撰寫：

```python
#urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('<slug:slug>/', views.post_detail, name='post_detail'),
]
```

```python
from django.shortcuts import get_object_or_404
from django.template.response import TemplateResponse
from .models import Post

def post_detail(request, slug):
    return TemplateResponse(request, 'blog/post_detail.html', {
        'post': get_object_or_404(Post.objects.all(), slug=slug),
    })
```

### shortcuts ＆ get_object_or_404

在 Django 框架中，get_object_or_404 是一個常用的「shortcuts」函數，用來簡化從資料庫查詢對象並處理找不到對象的情況。這個便捷函數屬於 django.shortcuts 套件。django.shortcuts 套件中的函數是跨越 MVC 層級的輔助工具，雖然這些工具引入了某種程度的耦合，但這種耦合是「受控的」，也就是說，這樣的設計不會對整個程式碼產生過大的影響。

設計這些「shortcuts」函數的核心是，雖然它們引入了控制層和模型層之間的耦合，但這種耦合是為了簡化程式碼且只會對局部產生影響。

例如，如果我們在視圖中使用 get_object_or_404，它幫助我們自動處理對象找不到的情況，並在需要時自動返回 HTTP 404 錯誤。而不是要求我們使用手動捕捉異常並引發 `ObjectDoesNotExist` 和 `Http404`。這樣，我們可以保持程式碼簡潔，不必每次都重複寫相同的錯誤處理邏輯，節省了時間和精力。

### 為何不直接在模型層處理？

如果我們選擇讓模型層自動引發 Http404 錯誤，這會導致模型層和視圖層之間的過度耦合，也就是說，模型層的設計會變得和視圖層綁定過緊。這違反了 Django 的設計原則之一——**保持鬆散耦合**。Django 的設計理念強調各個層級之間的相互獨立性，以便讓程式碼更具可維護性、可重用性和靈活性。

介紹完了 FBV，接下來看看 CBV，上面的 FBV 可以改寫成以下：

## CBV

```python
#urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('<slug:slug>/', views.PostDetailView.as_view(), name='post_detail'),
]
```

```python
from django.views.generic import DetailView
from .models import Post

class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/post_detail.html'
    context_object_name = 'post'
    slug_field = 'slug'
    slug_url_kwarg = 'slug' # 如果model欄位沒有設定SlugField就要加這行
```

Python 的設計哲學為「Explicit is better than implicit」（明瞭優於隱晦），主要的意思為「**當寫程式時，應該明確地表達意圖，而不是依賴隱晦或暗示的方式來達成目的**。」，程式的結構和邏輯應該是清晰易懂的，讓其他開發者或未來的自己可以很快理解它，而不是透過隱含的規則或約定去猜測程式的意圖。

簡單來說，要讓人一看就懂，不過 CBV 比較像是 Ruby on Rails 的「convention over configuration」（約定優於配置），框架自動配置許多細節，這樣開發者可以專注於業務邏輯，而不需要花太多時間設定各種配置。CBV 的優勢在於其繼承性和可擴展性，能夠避免重複程式，以及提供統一的邏輯結構。不過這樣子也代表，初學者需要花費比較多時間才能夠理解框架的自動配置。

### 應該選擇 CBV 還是 FBV 呢？

這其實要取決於專案的需求。簡單總結一下兩者的特點：

**FBV 更靈活，適合需要頻繁自定義的場景。**

**CBV 更簡潔，但可能隱藏太多邏輯，對於維護來說，增大了理解成本。**

這也是為什麼我想寫一系列 CBV 相關文章的原因之一。作為一個 Ruby on Rails 出身的開發者，CBV 的設計讓我感覺非常熟悉。

今天的分享就到這裡啦！下次我們再來探討更多 Django CBV 的使用細節～

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Django Views — The Right Way](https://spookylukey.github.io/django-views-the-right-way/index.html)

> [Django Docs - Generic display views](https://docs.djangoproject.com/en/5.1/ref/class-based-views/generic-display/)
