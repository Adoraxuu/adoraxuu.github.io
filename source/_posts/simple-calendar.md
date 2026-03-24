---
title: 'Ruby on Rails: 用Simple Calendar輕鬆打造客製化月曆'
layout: post
author: Adora Xu
date: 2024-01-14 12:18:03
tags:
- [ruby]
- [rails]
- [simple calendar]
- [gem]
- [Ruby on Rails]

categories:
- [Ruby on Rails]
---
<style>


.em-div{
border:2px #d4a373 solid;
margin:5px;
padding:10px 2px 2px 2px;
border-style:dashed;
}
</style>


<img src="/images/simple-calendar/banner.avif">

圖片來源：<a href="https://unsplash.com/photos/black-marker-on-notebook-zni0zgb3bkQ">Unsplash-Estée Janssens</a>
在製作專案的時候發現寶藏gem套件[simple_calendar](https://github.com/excid3/simple_calendar)(ﾉ>ω<)ﾉ，
可以輕鬆地製作月曆，而且客製化程度高，可以自定義CSS樣式，
這是我這次做出來的月曆：
<img src="/images/simple-calendar/5.png">

- 本次選擇使用[tailwind]([https://tailwindcss.com/](https://tailwindcss.com/)) CSS框架，安裝方法可以參考[tailwind installaion](https://tailwindcss.com/docs/installation)
- 亦可以使用bootstrap，安裝請參考：[bootstrap]([https://getbootstrap.com/](https://getbootstrap.com/))

---
### 安裝[simple_calendar](https://github.com/excid3/simple_calendar)
```ruby
$ bundle add simple_calendar
```

貼上後執行bundle i即可


### 製作新增會議的CRUD

因為demo所以使用meeting，可以自由選擇名稱，meeting會有開始時間＆結束時間，所以也增加`start_time` & `end_time`的column

因為新增會議不是本次demo的重點🤣使用scaffold會幫忙製作CRUD

```ruby
#terminal
rails g scaffold Consultation title description:text start_time:datetime e
nd_time:datetime
```

完成後執行`rails db:migrate`

### 建立主頁

```ruby
$ rails g controller pages home
```

### 在主頁自定義方法
```ruby
#home.html.erb
<%= month_calendar do |date| %>
  <%= date %>
<% end %>
```
除了Month Calendar，另外還有Week Calendar、Custom Length Calendar
客製化程度很高，可以依自己需要選擇


開啟 `rails s`就會出現月曆

<img src="/images/simple-calendar/2.png">

<div class="em-div">
補充：simple_calendar 預設開始時間的Model column是start_time，結束時間是end_time，
如果是別的column名稱，有兩個方法可以改變：
</div>

1. 在程式碼中加入attribute：
```ruby
<%= month_calendar(attribute: :你的column名稱, end_attribute: :你的column名稱) do |date| %>
  <%= date %>
<% end %>
```

2. 到model定義
```ruby
  def start_time
    start_date #假如你的結束時間名稱為start_date
  end

  def end_time
    end_date #假如你的結束時間名稱為end_date
  end
```


### 將`Meeting`加入月曆裡
只要改寫剛剛的程式碼即可

```ruby
#home.html.erb
<%= month_calendar(events: @meetings) do |date| %>
<%= date %>
<% end %>
```

### 定義`@meetings`

```ruby
#pages_controller.rb
def home
    @meetings = Meeting.where(
      star_time: Time.now.beginning_of_month.beginning_of_week..Time.now.end_of_month.end_of_week
    )
end
```

來說明一下這段程式碼的意思：
<div class="em-div">


使用`where`方法來檢索符合特定條件對象，
條件為：指定會議的開始時間應該在本月的開始到結束之間，
並且是在一周的開始到結束之間，這樣就可以得到當前月份內的會議。

簡單來說，我們使用`where`方法，獲取當前月份內的會議，並將這些會議存儲在 `@meetings` 變數中，以便在`view`中顯示。
</div>


<div class="em-div">

如果使用SQL語法，，其實更好理解
```SQL
SELECT * FROM meetings
WHERE start_time BETWEEN '開始時間' AND '結束時間';
```

「開始時間」：`Time.now.beginning_of_month.beginning_of_week` 生成的日期時間，
「結束時間」  `Time.now.end_of_month.end_of_week` 生成的日期時間，
這個查詢找會檢索所有在當前月份的第一週的開始時間到當前月份的最後一週的結束時間之間的會議。

</div>


這時候網頁的畫面會像這樣：
<img src="/images/simple-calendar/2.png">

為什麼還是一樣？因為Meeting沒有任何資料d(`･∀･)b

### 在Meeting裡增加一些資料
到`http://127.0.0.1:3000/meetings`，點選create新增
新增文完畢之後，回到`home.html.erb`，使用each do方法，將meetings顯示

```ruby
#home.html.erb
<%= month_calendar(events: @meetings) do |date, meetings| %>
  <%= date %>
  <% meetings.each do |meeting| %>
    <%= link_to meeting.title, meeting %>
    <%= meeting.description %>
  <% end %>
<% end %>
```

<div class="em-div">

補充：這裡需要輸入兩個資料，
`<%= month_calendar(events: @meetings) do |date, meetings| %>`
simple_calendar已經幫忙寫好方法，meetings會塞到相對應的時間內，
如果沒有寫的話meeting會重複印到每一天的表格內

</div>

這時畫面就會像這樣：
<img src="/images/simple-calendar/3.png">

做到這裡，基本上沒有樣式🤣，除了可以在`home.html.erb`內編輯樣式外，
另外可以使用 [simple_calendar](https://github.com/excid3/simple_calendar)內的客製化樣式

```ruby
$ rails g simple_calendar:views
```

這會在 app/views 中產生一個名為 simple_calendar 的資料夾，
可以根據自己的意願進行編輯。
執行之後可以看到他幫忙長出了這些資料：

<img src="/images/simple-calendar/4.png">

可以到application.css做一些自定義的編輯

```ruby
#application.css
.simple-calendar {
  .day {}

  .wday-0 {}
  .wday-1 {}
  .wday-2 {}
  .wday-3 {}
  .wday-4 {}
  .wday-5 {}
  .wday-6 {}

  .today {}
  .past {}
  .future {}

  .start-date {}

  .prev-month {}
  .next-month { }
  .current-month {}

  .has-events {}
}
```

也可以到simple_calendar幫忙長出來app/views/simple_calendar內調整預設的樣式
我是選擇直接到`_month_calendar.html.erb`修改，最後成果如下，應該有變得好看許多🤣
<img src="/images/simple-calendar/5.png">
我做了這些修改：

```ruby
#_month_calendar.html.erb
<div class="container mx-auto mt-10">
  <div class="wrapper bg-white rounded shadow w-full">
    <div class="simple-calendar">
      <nav>
        <div class="header flex justify-between border-b p-2">
          <span class="text-lg font-bold">
            <time datetime="<%= start_date.strftime('%Y-%m') %>" class="calendar-title"><%= t('date.month_names')[start_date.month] %> <%= start_date.year %></time>
          </span>

          <div class="buttons">
            <%= link_to t('simple_calendar.today', default: 'Today'), calendar.url_for_today_view %>

            <%= link_to calendar.url_for_previous_view do %>
              <button class="p-1">
                <svg width="1em" fill="gray" height="1em" viewBox="0 0 16 16" class="bi bi-arrow-left-circle" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
                  <path fill-rule="evenodd" d="M8 15A7 7 0 1 0 8 1a7 7 0 0 0 0 14zm0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16z"/>
                  <path fill-rule="evenodd" d="M8.354 11.354a.5.5 0 0 0 0-.708L5.707 8l2.647-2.646a.5.5 0 1 0-.708-.708l-3 3a.5.5 0 0 0 0 .708l3 3a.5.5 0 0 0 .708 0z"/>
                  <path fill-rule="evenodd" d="M11.5 8a.5.5 0 0 0-.5-.5H6a.5.5 0 0 0 0 1h5a.5.5 0 0 0 .5-.5z"/>
                </svg>
              </button>
            <% end %>

            <%= link_to calendar.url_for_next_view do %>
              <button class="p-1">
                <svg width="1em" fill="gray" height="1em" viewBox="0 0 16 16" class="bi bi-arrow-right-circle" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
                  <path fill-rule="evenodd" d="M8 15A7 7 0 1 0 8 1a7 7 0 0 0 0 14zm0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16z"/>
                  <path fill-rule="evenodd" d="M7.646 11.354a.5.5 0 0 1 0-.708L10.293 8 7.646 5.354a.5.5 0 1 1 .708-.708l3 3a.5.5 0 0 1 0 .708l-3 3a.5.5 0 0 1-.708 0z"/>
                  <path fill-rule="evenodd" d="M4.5 8a.5.5 0 0 1 .5-.5h5a.5.5 0 0 1 0 1H5a.5.5 0 0 1-.5-.5z"/>
                </svg>
              </button>
            <% end %>
          </div>
        </div>
      </nav>

      <table class="table table-striped w-full">
        <thead>
          <tr>
            <% date_range.slice(0, 7).each do |day| %>
              <th class="p-2 border-r h-10 xl:w-40 lg:w-30 md:w-30 sm:w-20 w-10 xl:text-sm text-xs">
                <span class="xl:block lg:block md:block sm:block hidden">
                  <%= t('date.abbr_day_names')[day.wday] %>
                </span>
              </th>
            <% end %>
          </tr>
        </thead>

        <tbody>
          <% date_range.each_slice(7) do |week| %>
            <tr class="text-center h-20">
                <% week.each do |day| %>
                  <%= content_tag :td, class: "border p-1 h-40 xl:w-40 lg:w-30 md:w-30 sm:w-20 w-10 overflow-auto transition cursor-pointer duration-500 ease hover:bg-gray-300" do %>
                    <% instance_exec(day, calendar.sorted_events_for(day), &passed_block) %>
                  <% end %>
                <% end %>
            </tr>
          <% end %>
        </tbody>
      </table>
    </div>
  </div>
</div>
```

```ruby
#home.html.erb
<h1 class="text-center m-5 text-xl">My calendar</h1>

<%= month_calendar(events: @meetings) do |date, meetings| %>
  <div class="flex flex-col h-40 mx-auto xl:w-40 lg:w-30 md:w-30 sm:w-full w-10 mx-auto overflow-hidden">
    <div class="top h-5 w-full">
      <span class="text-gray-500">
        <%= date.strftime('%m/%d') %>
      </span>
    </div>

    <% meetings.each do |meeting| %>
      <div class="bottom flex-grow py-1 w-full cursor-pointer">
        <div class="event bg-blue-400 text-white rounded p-1 text-sm mb-1">
          <%= link_to "#{meeting.start_time.strftime('%k:%M')} #{meeting.title}", meeting %>
        </div>
      </div>
    <% end %>
  </div>
<% end %>
```

simple_calendar其實也有把每個部份都定義了class名稱，
所以也可以到application.css檔案去更改套用，
但因為我使用tailwind，可以直接編輯html碼上的css，
後續優化可以再將重複的css樣式再放到application.css
或其他自定義的css即可，看個人習慣！

這樣就完成啦！是不是客製化程度非常高呢！
本篇文章是我由以下參考資料整理+自己製作而成，如果您有興趣了解更多，請參考：

### 參考資料：

>[simple_calendar](https://github.com/excid3/simple_calendar)

>[Create A Simple Calendar In Ruby On Rails 7](https://youtu.be/NhAGTL4ARfw?feature=shared)

> [Calendar Table Template](https://tailwindcomponents.com/component/calendar-table)
