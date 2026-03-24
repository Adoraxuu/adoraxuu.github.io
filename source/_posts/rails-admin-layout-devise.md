---
title: 'Ruby on Rails: 用Devise10分鐘製作管理者後台版面'
layout: post
author: Adora Xu
date: 2023-12-24 12:03:04
tags:
  - [devise]
  - [rails7]
  - [rails]
  - [layout]

categories:
  - [Ruby on Rails]
---
<style>
.em-div{
background-color: #d4a373;
color: white;
padding: 10px;
margin: 10px;
border-radius: 5px;
}

.em-div2{
border:2px #d4a373 solid;
margin:5px;
padding:10px 2px 2px 2px;
border-style:dashed;
}

.image-container {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* 陰影效果 */
  border-radius: 10px; /* 圓角效果 */
  overflow: hidden; /* 超出部分隱藏 */
  margin: 5px; /* 留白效果 */
}
</style>



###
<div class="image-container">
<img src="/images/rails_admin_layout_devise/banner.png">
</div>

這篇是個實作範例 ٩(●˙▿˙●)۶…⋆ฺ

主要內容都是參考以下影片而成，如果要了解更多可以看：

> [Layouts For Admin Users with Devise in Ruby on Rails 7](https://www.youtube.com/watch?v=SxwFyK9OtfY)

今天使用Rails來的[Devise](https://rubygems.org/gems/devise/versions/4.2.0?locale=zh-TW)套件來製作管理者後台版面

以下為實作步驟：

- new一個新專案，我名稱設為blog，也可以設定別的

```ruby
$ rails new blog
```

- 在Gemfile資料夾安裝devise，並執行`bundle install`

```ruby
#Gemfile
gem 'devise', '~> 4.2'
```

```ruby
$ bundle install
```

- 新增pages_controller＆home.html.erb

這裡也可以設定別的主題，只不過為了示範所以選擇這個，
<img src="/images/rails_admin_layout_devise/02.png">
這個指令會生出Pages的controller，裡面有home方法，view有home.html.erb頁面，routes也會幫忙長好pages/home，蠻方便的٩(๑❛ᴗ❛๑)۶

```ruby
$ rails g controller pages home
```

- 利用scaffold做CRUD的流程

用scaffold可以省掉好多步驟(´∀`)懶人救星

```ruby
$ rails g scaffold post title body:text
```
<div class="image-container">
<img src="/images/rails_admin_layout_devise/01.png">
</div>

- 設定routes

```ruby
#routes.rb
root 'pages#home'
```

- 安裝Devise & 生成使用者

```ruby
$ rails g devise:install
```

```ruby
$ rails g devise user
```

devise會自動幫你增加檔案，真的很方便

- 生一個migration，增加role欄位到User資料表

```ruby
$ rails g migration AddRoleToUsers role:integer
```

- 到User model增加角色，其中角色的名稱都可以自己定義，我使用admin

```ruby
#user.rb
enum role:[:general, :admin]
```

- 接著就可以到application_controller.rb，設置判斷使用者登入的layout頁面的`set_layout`方法

```ruby
#controller>application_controller
layout :set_layout # 設置判斷登入的使用者layout頁面

def set_layout
    if current_user&.admin?
      'admin'
    else
      'application'
    end
  end
```

`current_user&.admin?`

這行原本是這樣寫：`if current_user && current_user.admin == true`

可以用 &. 簡化

如果current_user存在（也就是有登入）以及 登入的角色是admin的話，layout就使用admin

如果不是的話， layout就使用預設導入application

- 接著到layout資料夾下，手動新增一個admin.html.erb，內容可以隨自己喜好調整

```ruby
#views>layout
新增 #admin.html.erb
<!DOCTYPE html>
<html>
  <head>
    <title>Blog</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body>
    <h2>此為管理者介面</h2>
    <%= yield %>
  </body>
</html>
```

接著可以到home.html.erb頁面，寫跳轉的判斷

```ruby
#home.html.erb
      <%#是否轉為店家後台判斷%>
      <% if current_user %>
        <%= current_user.role %>
          <%= link_to "logout", destroy_user_session_path, method: :delete, data: { turbo_method: :delete} %>
      <% else %>
          <%= link_to "log_in", new_user_session_path %>
      <% end %>
```

這個寫的地方可以自由選擇，如果要做個網站有很多頁面的話，

我也有嘗試放在_navbar.html.bar，讓整個網頁都可以套用此設定

接著就大功告成了！！

這是剛剛步驟做出的首頁
<div class="image-container">
<img src="/images/rails_admin_layout_devise/03.png">
</div>

登入頁面
<div class="image-container">
<img src="/images/rails_admin_layout_devise/04.png">
</div>

因為還沒設定current_user.role所以在html.erb頁面放`<% console %>`程式碼，

搭配指令，來快速指定某使用者的role
<div class="image-container">
<img src="/images/rails_admin_layout_devise/05.png">
</div>
```ruby
current_user

current_user.role = User.roles[:admin]

current_user.save

current_user.admin?
```

登入成功後，就有管理者的介面了！！
<div class="image-container">
<img src="/images/rails_admin_layout_devise/06.png">
</div>

實際應用的時候，可以把只有管理者知道的連結&資訊放在這，

讓他的介面看起來和一般使用者有區隔

使用Devise套件真的好方便٩(●˙▿˙●)۶…⋆ฺ 我原本想說自己刻好麻煩，

這整個步驟大約10分鐘就可以完成了！！

有興趣也可以試看看～～

---

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Layouts For Admin Users with Devise in Ruby on Rails 7](https://www.youtube.com/watch?v=SxwFyK9OtfY)