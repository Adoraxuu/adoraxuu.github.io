---
layout: post
title: 'Ruby on Rails : ActiveStorage連線Amazon S3'
author: Adora Xu
date: 2024-01-07 15:12:39
tags:
- [AWS]
- [Ruby on Rails]
- [active stroage]
- [amazon]
- [s3]
categories:
- [Ruby on Rails]

---

<img src="/images/active-storage-amazon-s3/01.avif">

> 圖片來源[Usplash](https://unsplash.com/photos/black-samsung-galaxy-smartphone-displaying-amazon-logo-rymh7EZPqRs)

### 

在使用Ruby on Rails製作網頁的時候，常常會使用Rails的 [Active Storage](https://edgeguides.rubyonrails.org/active_storage_overview.html#s3-service-amazon-s3-and-s3-compatible-apis)來儲存圖片，
讓網頁內容更加豐富٩(●˙▿˙●)۶…⋆ฺ主要使用方法可以參考 [Rails Guide](https://edgeguides.rubyonrails.org/active_storage_overview.html#s3-service-amazon-s3-and-s3-compatible-apis)的介紹

本篇主要介紹如何把Active Storage的資料儲存在Amazon S3，

因為網站常常有許多動態增長的檔案，最常見的就是使用者上傳的照片。
因為檔案很多很複雜，要處理這些檔案是一大難題。
我們可以利用上傳檔案到S3，來輕鬆的處理～
也能夠降低資料保存的風險（雞蛋不要放在同一個籃子的概念）

在找關於連接Amazon S3的資料時，其實花了一段時間，
這是目前覺得教學最詳細的是這個影片：
[Build a Blog with Rails Part 17: Handling File Uploads with ActiveStorage & Amazon S3](https://www.youtube.com/watch?v=UOLpv2f8mz8)
<iframe width="560" height="315" src="https://www.youtube.com/embed/UOLpv2f8mz8?si=eEjG_z97PKTQvtWR" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


本篇文章也會參考此影片來編寫，好的就讓我們開始吧 (^・ω・^ )
- 先到[AWS S3](https://aws.amazon.com/tw/pm/serv-s3/)註冊帳號
- 接著到S3 創建存放資料的地方，點選Create Bucket
<img src="/images/active-storage-amazon-s3/03.png">

- 選擇Bucket的所在地＆Bucket Name
<img src="/images/active-storage-amazon-s3/04.png">

依個人喜好而定，通常選離大部分使用者較近的位置，讀取速度會快一咪咪

- ACLs enabled
這個視需求而定而勾選，意思是說此Bucket的物件可以被其他 AWS 帳戶所擁有，並且可以透過 ACLs 來指定對該Bucket及其內部物件的訪問權限。
這意味著可以使用 ACLs 來管理誰可以讀取、寫入或刪除該儲存桶中的檔案，以及這些權限的具體配置。

- 存取權限 Access Settings

<img src="/images/active-storage-amazon-s3/05.png">

這部分是寫入資料的還有訪問的權限，因為能夠開放讓使用者上傳圖片，
所以我只勾選了下面兩項。

- 設定CORS
在Bucket的頁面點選Permission拉到最下面
可以直接複製Rails Guids上面的資料，並將網址改成本次測試的網址，
如果你有正式發佈的網址，也能講網址填寫在這裡
<img src="/images/active-storage-amazon-s3/CORS.png">
- 接著到[I AM](https://aws.amazon.com/tw/iam/) 建造使用者並新增Policy，為了設置訪問的權限及得到密鑰
<img src="/images/active-storage-amazon-s3/07.png">

選擇S3，並在下面勾選
- List : ListBucket
- Read: GetObj
- Write: PutObj,DeleteObj
- Permission management: PutObjAcl

這部分是參考[Rails Guide- Active Storage Overview](https://edgeguides.rubyonrails.org/active_storage_overview.html#s3-service-amazon-s3-and-s3-compatible-apis)的建議

<img src="/images/active-storage-amazon-s3/08.png">

Resource > Bucket 點選Add ARNs，填寫你剛剛創建的Bucket Name，讓Policy發揮效用
這邊我填寫兩個，除了這個bucket還有這個bucket之下的檔案都發揮效用
``````
adora-bucket
*adora-bucket/*
``````

接著下一頁填寫完policy名稱，按create policy，Policy就設定完成了！！🤩
- 創建使用者，並選擇剛剛選好的Policy
- 進入使用者頁面，點選Security credential，創建Access Key，讓我們的網站能夠透過此密鑰上傳圖片到S3
- 選擇可以在AWS以外使用
<img src="/images/active-storage-amazon-s3/09.png">
- 進入金鑰頁面

把金鑰儲存起來（id和secret_key都要），之後就不能訪問這個頁面，
所以很重要一定要存，接著前置步驟就大公告成了🤗

- 回到我們的專案，將storage.yml內，關於amazon的註解解掉，並輸入你的資料
```ruby
#storage.yml
amazon:
  service: S3
  access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>
  secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>
  region: us-east-1<替換成你的區域>
  bucket: adora-bucket<替換成的你的名稱>
```

- 接著在終端機輸入

```ruby
EDITOR="code --wait" rails credentials:edit
```

會開啟一個檔案檔名為crendentials.yml，可以將密鑰寫在這裡，必須要有相對應的master.key才能開啟這個檔案，github設定不會將master.key上傳～如果這個更動要分享給組員，將master.key私下傳給團隊成員，請他放在config資料夾底下即可

```ruby
aws:
  access_key_id: <你的key_id>
  secret_access_key: <你的密鑰>
```

- #development.rb設定將active_storage從:local改為:amazon
(如果要在正式環境使用，則需要更改production.rb)

```ruby
#development.rb
config.active_storage.service = :amazon
```

下載AWS S3的Gem

```ruby
#gemfile
gem "aws-sdk-s3", require: false
```

執行bundle i

這樣就大功告成了！！！
試著上傳圖片，可以看到後台顯示照片傳到s3

<img src="/images/active-storage-amazon-s3/12.png">

看s3的後台也看到圖片檔案
<img src="/images/active-storage-amazon-s3/11.png">


終於完成這篇了⸍⚙̥ꇴ⚙̥⸌應該是我史上圖片最多的一篇，
結果在上傳之後14張照片就消失了( ͒˃⌂˂ ͒)，
原來我不小心放在Public資料夾，而且下了Hexo cl指令  ◴_◶
可能老天爺想要我多熟練一點點，只好認命重新截圖...也刪除了一些不必要的圖片

最後希望這篇文章有幫助到你！有任何問題歡迎回覆，
本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [Build a Blog with Rails Part 17: Handling File Uploads with ActiveStorage & Amazon S3](https://www.youtube.com/watch?v=UOLpv2f8mz8)

> [Rails Guide- Active Storage Overview](https://edgeguides.rubyonrails.org/active_storage_overview.html#s3-service-amazon-s3-and-s3-compatible-apis)