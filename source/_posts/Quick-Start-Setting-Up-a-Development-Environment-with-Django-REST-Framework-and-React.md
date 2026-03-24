---
layout: post
title: '使用 Django REST Framework 和 React 建置開發環境'
date: 2024-09-15 00:00:00
tags: [Django, React, Django-REST, Frontend, Backend, Python]
categories: Django-REST
---

<img src="/images/Quick-Start-Setting-Up-a-Development-Environment-with-Django-REST-Framework-and-React/Main.webp">

## 開始之前

需要準備：Python (>=3.8)、Poetry（Django 套件管理）、NPM（React 套件管理）

## 建立 React

使用 Vite 建立 React 專案：

```bash
npm create vite@latest frontend -- --template react
cd frontend
npm install
npm run dev
```

前往 `http://localhost:5173/` 確認安裝成功。

## 建立 Django 專案

1. 安裝套件：Django、Django REST framework、django-cors-headers
2. 建立專案 `django-admin startproject core`，重新命名為 `backend`
3. 初始化 Poetry 環境並安裝依賴
4. 執行資料庫遷移：`python manage.py migrate`
5. 建立超級使用者，並建立一個 "Word" app

## 設定後端 API & APP

### 1. Django Settings

在 `settings.py` 中，將 `rest_framework`、`corsheaders` 和 `words` 加入 `INSTALLED_APPS`。設定 `CORS_ALLOW_ORIGINS` 允許 React 前端（`http://localhost:5173`）。

### 2. Word Model

```python
from django.db import models

class Word(models.Model):
    word = models.CharField(max_length=200, unique=True)
    pronunciation = models.CharField(max_length=100)
```

### 3. Admin Registration

在 `admin.py` 中註冊 `Word` model。

### 4. Django REST Framework 設定

在 `words` app 中建立 `api` 資料夾，包含 `serializers.py`、`urls.py` 和 `views.py`。

**Serializers**（資料轉 JSON）：

```python
from rest_framework.serializers import ModelSerializer
from ..models import Word

class WordSerializer(ModelSerializer):
    class Meta:
        model = Word
        fields = ['id', 'word', 'pronunciation']
```

**Views**（透過 ModelViewSet 實現 CRUD）：

```python
from rest_framework.viewsets import ModelViewSet
from ..models import Word
from .serializers import WordSerializer

class WordViewSet(ModelViewSet):
    queryset = Word.objects.all()
    serializer_class = WordSerializer
```

**URLs** — `words/api/urls.py`：

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import WordViewSet

word_router = DefaultRouter()
word_router.register(r'words', WordViewSet)
```

`core/api/urls.py`：

```python
from rest_framework.routers import DefaultRouter
from words.api.urls import word_router
from django.urls import path, include

router = DefaultRouter()
router.registry.extend(word_router.registry)

urlpatterns = [
    path('', include(router.urls)),
]
```

`core/urls.py`：

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('core.api.urls')),
]
```

### 5. 執行 Migrations 並啟動 Server

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
```

前往 `http://127.0.0.1:8000/api/words` 查看 browsable API。

## 設定 React 環境

### 1. 建立 `.env` 檔案

在 `frontend` 資料夾中建立：

```
VITE_API_URL=http://localhost:8000/api/
```

### 2. 使用環境變數

透過 `import.meta.env.VITE_API_URL` 在 `src/app.jsx` 中使用。

### 3. 從 API 取得資料

```javascript
import { useState, useEffect } from 'react'

function App() {
  const [data, setData] = useState([])

  useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch(`${import.meta.env.VITE_API_URL}/words/`);
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const data = await response.json();
        setData(data);
        console.log(data)
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    }
    fetchData();
  }, [])

  return (
    <div>
      <h1>Hello World</h1>
      {data.map((word) => (
        <div key={word.id}>
          <h2>{word.word}</h2>
          <p>{word.pronunciation}</p>
        </div>
      ))}
    </div>
  )
}

export default App
```

### 參考資料：

> [Vite](https://vitejs.dev/)
> [React](https://react.dev/)
> [Django](https://www.djangoproject.com/)
> [Django REST Framework](https://www.django-rest-framework.org/)
