---
layout: post
title: 'Ruby on Rails: 使用AWS IVS & OBS 輕鬆開始直播'
date: 2024-03-17 12:34:04
author: Adora Xu

tags:
- [AMAZON IVS]
- [OBS]
- [Ruby on Rails]
- [Ruby]
- [Rails]
- [livestream]

categories:
- [Ruby on Rails]

description: '教你如何在 Ruby on Rails 中整合 Amazon IVS 與 OBS，快速建立低延遲直播功能，涵蓋 IVS Channel 設定、串流金鑰配置與前端播放器嵌入。'
---
<img src="/images/ruby-aws-ivs-obs-stream-live/cover.avif">

圖片來源：[Usplash](https://unsplash.com/photos/black-smartphone-taking-photo-of-yellow-round-fruits-8zsBofKrhP8)

## AMAZON IVS
[Amazon IVS](https://aws.amazon.com/tw/ivs/)是AMAZON的互動式影片服務，可以利用此構建即時串流和互動影片體驗。

本篇文章主要參考 [Deanin - Livestream to Your Rails App with Amazon IVS](https://www.youtube.com/watch?v=aY4d5kNKbGc) ＆ [Amazon Interactive Video Service](https://docs.aws.amazon.com/ivs/latest/LowLatencyUserGuide/web-getting-started.html)

## 建立IVS Channel

AWS IVS的服務超過一定額度會需要收費，收費標準可以參考這裡：[Amazon 互動式影片服務定價](https://aws.amazon.com/tw/ivs/pricing/)，這次使用的範圍在免費額度內。

<img src="/images/ruby-aws-ivs-obs-stream-live/1.png">

打上Channel name即可，下方亦有可以將直播影片儲存至S3等選項，可依照個人需要調整

建置完成就會看到這個畫面：
<img src="/images/ruby-aws-ivs-obs-stream-live/2.png">

## 建置Ruby on Rails專案

```ruby
$ rails new video
$ rails g controller pages home
$ rails g scaffold post title body:text
```

為了方便可以將`config/routes.rb` 增加  `root 'pages#home’`

## 串接AWS IVS直播

參考：[AWS IVS Player SDK](https://docs.aws.amazon.com/ivs/latest/LowLatencyUserGuide/web-getting-started.html)

最重要的部分就是這段，記得將PLAYBACK_URL替換成要載入的URL
<img src="/images/ruby-aws-ivs-obs-stream-live/3.png">


要載入的URL：
<img src="/images/ruby-aws-ivs-obs-stream-live/4.png">


```ruby
#app/views/pages/home.html.erb
<h1>Pages#home</h1>
<p>Find me in app/views/pages/home.html.erb</p>

<script src="https://player.live-video.net/1.26.0/amazon-ivs-player.min.js"></script>
<div id="amazon-ivs">
    <video id="video-player"
        autoplay
        controls
        playsinline
        style="max-height: 500px; max-width: 500px"
    ></video>
</div>
<script>
  if (IVSPlayer.isPlayerSupported) {
    const player = IVSPlayer.create();
    player.attachHTMLVideoElement(document.getElementById('video-player'));
    player.load("Playback URL");
    player.play();
  }
</script>
```

## 設定OBS

有在接觸直播的人應該蠻熟悉OBS的，OBS是一個**用於錄影和直播的自由開源軟體，**常見的Youtube, Twitch,FB, Instagram都可以接此直播，自訂的功能也很高，或是也可以像我們這次一樣，使用AWS IVS + OBS 放到自己的網頁專案裡，可以參考和下載：[OBS](https://obsproject.com/)

OBS → Setting → Stream → 複製自己AWS Channel的server & key貼上
<img src="/images/ruby-aws-ivs-obs-stream-live/5.png">


AWS IVS Server & Key
<img src="/images/ruby-aws-ivs-obs-stream-live/6.png">


接著在OBS 按下Star Stream就大功告成了！

AWS Channel:
<img src="/images/ruby-aws-ivs-obs-stream-live/7.png">


網頁：
<img src="/images/ruby-aws-ivs-obs-stream-live/8.png">


原本想說串接直播不知道會不會很難，看了[Deanin - Livestream to Your Rails App with Amazon IVS](https://www.youtube.com/watch?v=aY4d5kNKbGc) 的教學發現透過AWS IVS變得簡單很多！

本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料：

>  [Deanin - Livestream to Your Rails App with Amazon IVS](https://www.youtube.com/watch?v=aY4d5kNKbGc) 

> [Amazon Interactive Video Service](https://docs.aws.amazon.com/ivs/latest/LowLatencyUserGuide/web-getting-started.html)

> [AWS IVS Player SDK](https://docs.aws.amazon.com/ivs/latest/LowLatencyUserGuide/web-getting-started.html)

> [OBS](https://obsproject.com/)
