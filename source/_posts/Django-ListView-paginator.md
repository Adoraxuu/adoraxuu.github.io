---
layout: post
title: 'Django: ListView & paginator'
date: 2024-10-13 00:00:00
tags: [Django, FBV, CBV, ListView]
categories: Django-CBV
---

<img src="/images/Django-ListView-paginator/01.webp">

## FBV

要顯示部落格文章列表，使用 FBV 可以這樣寫：

```python
def post_list(request):
    posts = Post.objects.all()
    paginator = Paginator(posts, 10)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return TemplateResponse(request, 'blog/post_list.html', {
        'page_obj': page_obj,
    })
```

`Paginator` 類別提供分頁功能，`get_page()` 回傳一個 Page 物件，包含以下分頁資訊：

- `page_obj.has_previous`：是否有上一頁
- `page_obj.has_next`：是否有下一頁
- `page_obj.paginator.num_pages`：總頁數
- `page_obj.number`：目前頁碼

為了減少重複，可以將分頁邏輯抽出成可重用的輔助函數：

```python
def post_list(request):
    posts = Post.objects.all()
    context = {
        'posts':posts
    } | paged_object_list_context(request, posts, paginate_by=10)
    return TemplateResponse(request, 'blog/post_list.html', context)

def paged_object_list_context(request, object_list, paginate_by):
    paginator = Paginator(object_list, paginate_by)
    page_number = request.GET.get('page', 1)
    page_obj = paginator.get_page(page_number)
    return {
        'page_obj': page_obj,
    }
```

## CBV

使用 class-based ListView 可以大幅簡化：

```python
class PostListView(ListView):
    model = Post
    paginate_by = 10
```

這取代了大量 FBV 程式碼，展現了「convention over configuration」的原則。

## 簡短一定更好嗎？

CBV 與 FBV 的取捨：

- **簡潔 vs. 擴展性** — CBV 在處理像分頁這種直觀的 view 時表現出色，但當需要加入自定義邏輯（權限過濾、統計計算、複雜表單處理）時，就需要深入了解內部機制，大量使用 Mixin 或覆寫方法。

- **CBV 的隱藏複雜性** — 看似乾淨的程式碼背後，隱藏著難以追蹤的邏輯。修改預設行為往往需要覆寫多個內部方法或組合 Mixin，增加學習成本與程式碼複雜度。

- **如何選擇 FBV 和 CBV** — 當 view 邏輯簡單且固定時，CBV 確實提供了便利。但隨著業務邏輯複雜度增加，FBV 的靈活性和控制力更佳。**合理的結構和清晰的邏輯，才是程式碼真正的價值。**

### 參考資料：

> [Django Views — The Right Way](https://spookylukey.github.io/django-views-the-right-way/index.html)

> [Django Docs](https://docs.djangoproject.com/en/5.1/)
