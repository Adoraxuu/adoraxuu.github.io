---
layout: post
date: 2024-05-15 22:28:06
title: 'Django : 從零開始學 7 - 增加評論功能'
author: Adora Xu
tags:
- [Django]
- [Python]
- [form]
- [comment]

categories:
- [Django]
---

文章建立好後，也來增加評論表單，讓使用者可以留言吧～

## 增加 comment model

```python
class Comment(models.Model):
    post = models.ForeignKey( #增加ForeignKey，讓他和post有關聯
        Post,
        on_delete=models.CASCADE, #post被刪除，關聯的comment也會被刪除
        related_name='comments',
    ) 
    username = models.CharField(max_length=80)
    email = models.EmailField()
    body = models.TextField()
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    active = models.BooleanField(default=True) #是否顯示

    class Meta:
        ordering = ('-created',)#排序規則，新的在上面

    def __str__(self): #debug的時候，可以印出來的格式
        return 'Comment by {} on {}'.format(self.username, self.post)
```

`$ poetry run python manage.py makemigrations blog`

<img src="/images/django-add-comment/0.png">

`$ poetry run python manage.py migrate`

<img src="/images/django-add-comment/1.png">


可以看到DB裡blog_comment的Table建立好了

<img src="/images/django-add-comment/2.png">


## 更新Admin介面

```python
from django.contrib import admin
from .models import Post, Comment #新增這行

#...其他程式

#新增以下
@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    list_display = ('name', 'email', 'post', 'created', 'active')
    list_filter = ('active', 'created', 'updated')
    search_fields = ('name', 'email', 'body')
```

後台就可以看到更新後的admin介面

<img src="/images/django-add-comment/3.png">


## 透過ORM操作

```bash
$ python manage.py shell
$ from blog.models import Post, Comment
$ post = Post.objects.all()
```

來看一下目前post是什麼：

```bash
>>> post
<QuerySet [<Post: Lorem Ipsum2>, <Post: Lorem Ipsum>, <Post: created from shell>, <Post: hi>]>
```

在最後一篇post新增評論

```bash
>>> from blog.models import Post, Comment
>>> post = Post.objects.all()
>>> post
<QuerySet [<Post: Lorem Ipsum2>, <Post: Lorem Ipsum>, <Post: created from shell>, <Post: hi>]>
>>> post = post.last()
>>> post
<Post: hi>
>>> com = Comment(post=post, username='text', email='text@gmail.com', body='here is a comment from shell')
>>> com.save()
>>> com
<Comment: Comment by text on hi>
```

也可以透過`.filter(id=).first()`找到想要新增的文章

先使用for循環印出所有文章的id，在使用filter找指定的文章

```bash
>>> post = Post.objects.all()
>>> for p in post:
...     print(p.id)
... 
7
6
5
3
>>> post = Post.objects.filter(id=5).first()
>>> com = Comment(post=post,username='Amy',email='Amy@gmail.com',body='this is comment from Amy')
>>> com.save()
>>> com
<Comment: Comment by Amy on created from shell>
```

也可以看到DB的blog_comment Table裡面有資料：

<img src="/images/django-add-comment/4.png">


可以從post調出comments

```bash
>>> post = Post.objects.filter(id=5).first()
>>> post.comments.all()
<QuerySet [<Comment: Comment by Amy on created from shell>]>
>>> for c in post.comments.all():
...     print(c)
... 
Comment by Amy on created from shell
```

## 將頻論放到blog前台

```bash
#project/blog/templates/blog/detail.html
{% block content %}
    <h1>{{ post.title }}</h1>
    <p>Published {{ post.published_date }} by {{ post.author }}</p>
    {{ post.body|linebreaks }}

    {% with post.comments.count as total_comments %}
        <h2>{{ total_comments }} comment{{ total_comments|pluralize }}</h2>
    {% endwith %}

    {% for comment in post.comments.all %}
        <div>
            <p>Comment by {{ comment.username }}{{ comment.created }}</p>
            {{ comment.body|linebreaks }}
        </div>
    {% empty %}
        <p>There are no comments yet.</p>
    {% endfor %}
{% endblock %}
```

- `comment{{ total_comments|pluralize }}`：如果是複數就會+s
- `{% for comment in post.comments.all %}` ：使用for循環帶出所有的評論
- `{% empty %}` ：如果沒有頻論就顯示  `<p>There are no comments yet.</p>`

做到這步驟，前台看起來就會像這樣，評論顯示在文章的下方

<img src="/images/django-add-comment/5.png">

<img src="/images/django-add-comment/6.png">


## 使用者輸入表單(form)

來建立一個表單讓使用者可以輸入評論內容

## 與model關聯

```bash
# project/blog/forms.py
from django import froms
from .models import Comment

class CommentForm(froms.ModelForm):
    class Meta:
        model = Comment #與DB的Comment Model關聯
        fields = ('name', 'email', 'body') #傳進來的資料
```

這步主要是能讓前台傳的資料與DB關聯

## 編輯views，以控制表單要傳遞的內容

```bash
# project/blog/views.py
def detail(request, year, month, day, slug):
#... 其他程式

    # List of active comments for this post
    comments = post.comments.filter(active=True) #過濾出所有active的comment
    new_comment = None
    if request.method == 'POST': #如果表單發送post請求
        comment_form = CommentForm(data=request.POST) # 讀取form數據，產生comment_form對象
        if comment_form.is_valid(): #驗證是否正確
            new_comment = comment_form.save(commit=False) #新建一個new_comment對象
            new_comment.post = post
            new_comment.save()
    else:
        comment_form = CommentForm() #如果不是post 是get，就做一個form的顯示

    return render( # return Template
        request,
        'blog/detail.html',
        {'post': post,
         'comments': comments, # 傳遞comment的資料到template
         'new_comment': new_comment,
         'comment_form': comment_form
         }
    )

#... 其他程式

```

## 編輯前台顯示畫面

```ruby
#project/blog/templates/blog/detail.html

#...其他程式
    {% if new_comment %}
    <h2>Your comment has been added.</h2>
    {% else %}
    <h3><a name="commentform"></a>Leave a Comment</h3>
    <form action="#commentform" method="post">
        {{ comment_form.as_p }}
        {% csrf_token %}
        <p><input type="submit" value="Add comment"></p>
    </form>
    {% endif %}
    
#...其他程式
```

- `{{ *comment_form*.*as_p* }}：`顯示文字會用html的<p>…<p>包住
- `{% csrf_token %}`：防止corss site攻擊

以上都做好後，按右鍵查看原始碼可以看到，Django幫我們做了一個form

<img src="/images/django-add-comment/7.png">


前台也能夠順利使用表單新增留言了～

<img src="/images/django-add-comment/8.png">


本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Django Docs - Working with forms](https://docs.djangoproject.com/en/5.0/topics/forms/)

> [Django 2 Web开发入门与实战](https://www.udemy.com/course/django-2-web/)
