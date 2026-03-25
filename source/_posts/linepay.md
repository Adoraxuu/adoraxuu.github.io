---
layout: post
title: 'Ruby on Rails: 串接LINE Pay API v3'
author: Adora Xu
date: 2024-01-21 12:47:26
tags:

- [Ruby]
- [Ruby on Rails]
- [line pay]
- [API]
- [line pay API]
- [line pay v3]
- [v3]
- [faraday]
- [金流]

categories:
- [Ruby on Rails]
description: '完整教學如何在 Ruby on Rails 中串接 LINE Pay API v3 金流，使用 Faraday 發送請求，涵蓋 sandbox 測試到正式環境的完整流程。'
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
</style>

###

<img src="/images/banner.jpeg">

> 圖片來源: [LINE Pay新聞](https://pay.line.me/portal/tw/customer/press/98766?categoryId=)

### 

今天來使用Ruby on Rails串接LINE Pay ฅ^•ﻌ•^ฅ

首先需要到[LINE Pay  SandBox](https://pay.line.me/th/developers/techsupport/sandbox/creation?locale=zh_TW)申請一個測試帳號

<img src="/images/LINE-Pay/2.png">

接著到信箱收LINE Pay 發的帳號密碼，登入可以從這裡登入：[LINE Pay測試流程](https://pay.line.me/tw/developers/techsupport/sandbox/testflow?locale=zh_TW)

接著可以到[LINE Pay 測試環境](https://sandbox-pay.line.me/zh_TW/payment/interlockKey)，拿所需的Channel ID ＆ Channel Secret Key

<img src="/images/LINE-Pay/3.png">

然後把[LINE Pay API](https://pay.line.me/tw/developers/apis/onlineApis?locale=zh_TW)技術手冊拿緊緊，因為接下來會很需要他(ㆆᴗㆆ)

我先使用credientail將所需要的資料&機密資料放進去

`EDITOR="code --wait" rails credentials:edit`

- ENV

```ruby
line:
  CHANNEL_ID: <your channel id>
  SECRET_KEY: <your secret key>
  LINEPAY_SITE: https://sandbox-api-pay.line.me
  CONFIRM_URL: /orders/confirm
  CANCEL_URL: /orders/cancel
  DOMAIN_NAME: http://127.0.0.1:3000
  VERSION: v3
```

CHANNEL_ID ＆ SECRET_KEY 要替換成剛剛從LINE Pay SANBOX查到的ID和KEY

寫在credential的資料有幾個好處：

1. 高機密性：必須要有對應的key才能夠打開，此key不會跟著上傳GitHub
2. 修改方便：金流的版號會不斷更新（這次是接v3），如果之後更新從crediential改就好
3. 確保正確性：串接金流只要有一個東西錯誤就會失敗，將資料集中寫在這裡，確認正確性時會比較方便

我使用gem [faraday](https://rubygems.org/gems/faraday/versions/2.7.11?locale=zh-TW)向LINE Pay 發送請求，所以這邊先安裝

```ruby
bundle add "faraday"
```

```ruby
bundle i
```

建一個專案來做LINE Pay 串接٩(●˙▿˙●)۶…⋆ฺ

```ruby
$ rails new project_name
$ rails g scaffold product name price:integer
$ rails g model order
$ rails g controller orders
#seed
Product.create(
  name: "測試商品",
  price: 500
)

$ rails db:create db:migrate db:seed
```

本次要做：LINE Pay 的付款(create) ＆ LINE Pay 回覆的付款結果(confirm)

預計會寫在orders裡面，所以routes可以這樣寫：

```ruby
#routes.rb
root "products#index"

  resources :products

  resources :orders do
    collection do
      get :confirm
    end
  end
```

首頁建立一個送出資料的表單

```ruby
#index.html.erb
<%= form_tag orders_path, method: :post, data: { turbo: false } do %>
    <%= hidden_field_tag(:name, @product.name) %>
    <%= hidden_field_tag(:price, @product.price) %>
    <%= select_tag(:quantity, options_for_select([1])) %>
    <%= submit_tag "送出", name: nil, class: "btn btn-primary" %>
<% end %>
```

前置作業都做好了，可以開始串啦！

想要串接LINE Pay 前，要先了解LINE Pay 那邊會需要什麼資料

可以先看到手冊裡LINE Pay request的部分，Requirement Y的部分都是LINE Pay 需要的

簡單來說，會需要head & body（好像廢話），但裡面的內容格式定義不同

這是Head:

<img src="/images/header.jpg">

這個是Body:

<img src="/images/LINE-Pay/5.png">


看完這麼多東西一定頭昏眼花，沒關係我們直接動手做ʕ •̀ o •́ ʔ

```ruby
#orders_controller
def create
    # body
    order_id = "order#{Time.now.strftime('%Y%m%d%H%M%S')}"
    packages_id = "package#{SecureRandom.uuid}"
    amount = params[:quantity].to_i * params[:price].to_i

    body = { amount:,
             currency: 'TWD',
             orderId: order_id,
             packages: [{ id: packages_id,
                          amount:,
                          products: [{
                            name: params[:name],
                            quantity: params[:quantity].to_i,
                            price: params[:price].to_i,
                          }] }],
             redirectUrls: { confirmUrl: "#{Rails.application.credentials.line.DOMAIN_NAME}#{Rails.application.credentials.line.CONFIRM_URL}",
                             cancelUrl: "#{Rails.application.credentials.line.DOMAIN_NAME}#{Rails.application.credentials.line.CANCEL_URL}"} }
    # header
    signature_uri = "/#{Rails.application.credentials.line.VERSION}/payments/request"
    create_header(signature_uri, body)
    conn = Faraday.new(
      url: "#{Rails.application.credentials.line.LINEPAY_SITE}/#{Rails.application.credentials.line.VERSION}/payments/request",
      headers: @header
    )

    response = conn.post do |req|
      req.body = body.to_json
    end
    parsed_response = JSON.parse(response.body)
    if parsed_response['returnCode'] == '0000'
      redirect_to parsed_response['info']['paymentUrl']['web'], allow_other_host: true
    else
      redirect_to order_path(@order), notice: t(:payment_failed, scope: %i[message])
      puts parsed_response
    end
  end 
```

最一開始做body，其實就是把LINE Pay 技術文件裡，body有打勾為必需的項目，

使用他指定的格式組合，如果在一般專案order model的話，也能夠從order裡面撈資料

因為request和confirm的`signature_uri` 和 `body`  不同，

所以我沒有特別拉出去做一個方法，而是在create, confirm內個別組合，

然後傳出給create_header

## creat_header

建立一個 HTTP 請求的標頭（header），以便進行身份驗證和授權。

因為confirm也會需要再製作一次，所以我特地拉出來另一個方法：

```ruby
#orders_controller
def create_header(signature_uri, body)
    nonce = SecureRandom.uuid
    secret = Rails.application.credentials.line.SECRET_KEY
    message = "#{secret}#{signature_uri}#{body.to_json}#{nonce}"
    hash = OpenSSL::HMAC.digest(OpenSSL::Digest.new('sha256'), secret, message)
    @signature = Base64.strict_encode64(hash)
    @header = { 'Content-Type': 'application/json',
                'X-LINE-ChannelId': "#{Rails.application.credentials.line[:CHANNEL_ID]}", # rubocop:disable Style/RedundantInterpolation
                'X-LINE-Authorization-Nonce': nonce,
                'X-LINE-Authorization': @signature }
  end
```

其實這段做的就是手冊裡的這個步驟：

<div class="em-div2">

**HTTP Method : POST**

Signature = Base64(HMAC-SHA256(Your ChannelSecret, (Your ChannelSecret + URI + RequestBody + nonce)))

</div>

這邊不用另外匯入加密套件，使用Ruby on Rails內建的即可

另外LINE Pay  v3因為要防止惡意攻擊，所以要求每次送header要有一個唯一的 nonce，我使用 `SecureRandom.uuid` 生成。

加密的部分：

- 透過OpenSSL ，使用 HMAC和 SHA-256 加密算法，得到 `hash`。
- 使用 Base64 加密方式將 `hash` 轉換成字符串，得到 `@signature`。

上面兩步做完，就可以成功跳轉到LINE PAY頁面

<img src="/images/LINE-Pay/6.png">


使用LINE Pay 掃描完成就會跳轉到確認頁面

確認頁面同時也要使用POST方法，串接confirmAPI，

confirm要求的body會比較少，但header的要求是一樣的，

所以在這裡一樣呼叫create_header，

因為本次是demo，所以金額固定為500，

如果是在專案可以利用上一步request成功後

LINE Pay 回傳的`transactionId` ,`orderId`來找該筆訂單

```ruby
def confirm # rubocop:disable Metrics/MethodLength
    transaction_id = params[:transactionId]
    amount = 500.to_i

    body = {
      amount:,
      currency: 'TWD'
    }
    signature_uri = "/#{Rails.application.credentials.line.VERSION}/payments/#{transaction_id}/confirm"
    create_header(signature_uri, body)

    conn = Faraday.new(
      url: "#{Rails.application.credentials.line.LINEPAY_SITE}/v3/payments/#{transaction_id}/confirm",
      headers: @header
    )

    response = conn.post do |req|
      req.body = body.to_json
    end
    parsed_response = JSON.parse(response.body)
    puts "Confirm Response: #{response.body}"

    if parsed_response['returnCode'] == '0000'
      redirect_to root_path, notice: '付款成功'
    else
      redirect_to root_path, notice: '付款失敗'
    end
  end
```

接著再重新測試，按送出後，會到LINE Pay 付款頁

<img src="/images/LINE-Pay/7.png">

掃描成功案付款後，就會到付款結果頁：

<img src="/images/LINE-Pay/8.png">

LINE Pay 測試後台也會出現資料

<img src="/images/LINE-Pay/9.png">


完成啦٩(●˙▿˙●)۶ 因為是第一次串LINE Pay ，

所以來來回回確認了好幾次資料，

因為只要有一個地方錯誤就會失敗，找得眼睛都花了(✖﹏✖)

這篇內容主要以LINE Pay 官方技術文件還有以下參考資料生出來的٩(๑❛ᴗ❛๑)۶

如果有需要更正的地方歡迎指教！

如果您有興趣了解更多，請參考：

### 參考資料：

>[LINE Pay Developers APIs](https://pay.line.me/th/developers/apis/onlineApis?locale=zh_TW)

>[faraday](https://github.com/lostisland/faraday)

>[金流自己串，使用 Node.js 串接 LINE Pay](https://www.youtube.com/watch?v=nEjVIeDE-Ro)

> [D-2.Line_pay_api 串接(三) Rails 串接](https://ithelp.ithome.com.tw/articles/10273803)
