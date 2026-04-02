---
title: "前後端分離實戰：用 Node.js + React + MongoDB 建立筆記應用"
preview: "經過 AI 夥伴的指導和親手實作，終於理解了全棧應用的核心概念。本文記錄完整的學習過程。"
permalink: "/articles/2026-04-02"
date:   2026-04-02 15:23:06
layout: post
tags:
  - "javascript"
  - "nodejs"
  - "react"
  - "mongodb"
  - "fullstack"
---

今天請我的 AI 夥伴用簡單的 demo project 教我怎麼一步一步做。經過幾個小時的互動和動手實作，理解了前後端分離的核心概念，以及如何從零開始建立一個完整的全棧應用。

## 為什麼要學前後端分離？

在開始之前，我問了自己為什麼要學這個？答案很簡單：

- **獨立開發** - 前端和後端可以由不同的開發者同時進行
- **多終端支援** - 同一套 API 可以服務網頁、App、甚至第三方應用
- **易於維護和擴展** - 修改前端邏輯不會影響後端，反之亦然
- **工業標準** - 現代web應用幾乎都採用這種架構

## 我們的技術棧選擇

根據我的開發經驗，我和AI夥伴選擇了以下技術組合：

- **前端**: React 19 + Vite
- **後端**: Node.js + Express
- **資料庫**: MongoDB Atlas
- **通訊協議**: REST API (HTTP/JSON)

這個組合的優點是：全 JavaScript TechStack、學習曲線平緩、社群活躍、部署容易。

## 系統架構一覽

在開始寫程式碼前，我們先設計整個架構：

```
┌─────────────────────────────────────┐
│     瀏覽器 (React 前端)              │
│  - 展示筆記列表                      │
│  - 新增/編輯/刪除筆記的表單          │
└────────────┬────────────────────────┘
             │ HTTP 請求 (JSON)
             │
┌────────────▼────────────────────────┐
│   Node.js Express 伺服器 (API)       │
│  - GET /api/notes (取得所有筆記)     │
│  - POST /api/notes (新增筆記)        │
│  - PUT /api/notes/:id (編輯筆記)     │
│  - DELETE /api/notes/:id (刪除筆記)  │
└────────────┬────────────────────────┘
             │ MongoDB 查詢
             │
┌────────────▼────────────────────────┐
│    MongoDB (資料庫)                  │
│  - 儲存所有筆記 (標題、內容、日期)  │
└─────────────────────────────────────┘
```

**重點**：前端和後端通過 HTTP API 溝通，完全解耦。前端不直接訪問資料庫，後端也不知道前端如何渲染。

## 環境檢查清單

在開始編寫代碼前，我先確保開發環境準備就緒：

```bash
# 檢查 Node.js 和 npm
node --version  # 我的版本: v25.8.2
npm --version   # 我的版本: 11.11.1
```

如果沒有安裝，可以到 [nodejs.org](https://nodejs.org) 下載。

另外，我使用了 **MongoDB Atlas**（免費雲端資料庫），省去了本地 MongoDB 安裝的麻煩。

## 第一部分：建立後端 (Express + MongoDB)

### 步驟 1: 初始化 Node.js 專案

```bash
mkdir notes-app
cd notes-app
mkdir backend
cd backend
npm init -y
```

這會建立一個 `package.json` 檔案。

### 步驟 2: 安裝依賴

```bash
npm install express mongoose cors dotenv
```

**各個套件的作用**：
- **express**: Web 框架，用於建立伺服器和路由
- **mongoose**: MongoDB 驅動，提供 schema 定義和查詢語法
- **cors**: 跨域資源共享，允許前端從不同域訪問我們的 API
- **dotenv**: 環境變數管理，用於存儲敏感信息（如資料庫密碼）

## 後續：前端開發

這是完整教學的第一部分。在下一篇，我將展示如何：

1. 建立 React + Vite 前端項目
2. 使用 fetch API 連接到我們的後端
3. 構建 NoteList 和 NoteForm 元件
4. 完成整個前後端整合

敬請期待！

---

**我的心得**：前後端分離一開始看起來複雜，但當實際動手做一遍後，就會明白這種設計模式的優雅之處。AI學習的旅程，學習愉快！
