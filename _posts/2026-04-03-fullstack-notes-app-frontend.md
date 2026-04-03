---
title: "前後端分離實戰（二）：用 React + Vite 建立互動前端"
preview: "後端 API 建好了，這次換前端登場。從 Vite 專案建立、元件設計到資料流串接，完整記錄 React 前端的實作過程。"
permalink: "/articles/2026-04-03"
date:   2026-04-03 10:00:00
layout: post
tags:
  - "javascript"
  - "react"
  - "fullstack"
  - "nodejs"
---

上一篇我們用 Express + MongoDB 建好了後端 API。這次，AI 夥伴帶我繼續把前端補完。

動手之前，AI 夥伴說了一句讓我印象深刻的話：「後端是處理**資料**，前端是處理**使用者體驗**。」

我把這句話記下來，整個前端開發過程，都在驗證這件事。

## 為什麼選擇 React？

在開始寫之前，我和 AI 夥伴討論了幾個選項。最後選 React 的原因：

1. **元件化設計** - 把 UI 拆成小元件，便於管理和重用
2. **Hooks API** - `useState` 和 `useEffect` 讓狀態管理直覺很多
3. **Vite 熱更新** - 修改代碼立即看到結果，開發體驗很好
4. **業界普及** - 學了能直接用在實際工作上

## 前端的三層架構

在寫任何元件之前，AI 夥伴先帶我畫出整體架構：

```
┌─────────────────────────────────┐
│     UI 層 (React 元件)           │
│  - App.jsx (主應用)              │
│  - NoteForm.jsx (表單)           │
│  - NoteList.jsx (列表)           │
└────────────┬────────────────────┘
             │
┌────────────▼────────────────────┐
│     邏輯層 (狀態管理)             │
│  - useState (本地狀態)           │
│  - useEffect (副作用處理)        │
│  - 載入狀態、錯誤處理            │
└────────────┬────────────────────┘
             │
┌────────────▼────────────────────┐
│     API 層 (服務)                │
│  - api.js (所有 HTTP 請求)       │
│  - 統一的錯誤處理                │
│  - 易於修改伺服器地址            │
└─────────────────────────────────┘
```

這個分層思路和後端的 MVC 概念一脈相承——每一層只做自己的事，不越界。

## 第二部分：建立前端（React + Vite）

### 步驟 1: 建立 Vite 專案

```bash
npm create vite@latest frontend -- --template react
cd frontend
npm install
```

AI 夥伴解釋為什麼用 Vite 而不是 Create React App：

- ⚡ **極快啟動** - 約 300ms
- 🔄 **即時 HMR** - 修改代碼立刻看到變化
- 📉 **輕量打包** - 生產環境 bundle 更小

### 步驟 2: 建立 API 服務層

在寫任何 UI 元件前，AI 夥伴要我先建立 `services/api.js`，把所有 API 請求集中管理：

```javascript
const API_URL = 'http://localhost:5000/api';

export const notesAPI = {
  getAll: async () => {
    const response = await fetch(`${API_URL}/notes`);
    if (!response.ok) throw new Error('取得筆記失敗');
    return await response.json();
  },

  create: async (title, content) => {
    const response = await fetch(`${API_URL}/notes`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title, content }),
    });
    if (!response.ok) throw new Error('新增筆記失敗');
    return await response.json();
  },

  update: async (id, title, content) => { /* ...相同模式 */ },
  delete: async (id) => { /* ...相同模式 */ },
};
```

這個設計的好處：要換後端 URL 時，只需改一個地方的 `API_URL`。

### 步驟 3: NoteForm 元件

表單元件同時處理「新增」和「編輯」兩種模式，透過 `editingNote` prop 判斷：

```javascript
export default function NoteForm({ onNoteAdded, editingNote, onEditComplete }) {
  const [title, setTitle] = useState(editingNote?.title || '');
  const [content, setContent] = useState(editingNote?.content || '');
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!title.trim() || !content.trim()) {
      setError('標題和內容都是必填的');
      return;
    }
    setIsLoading(true);
    try {
      if (editingNote) {
        await notesAPI.update(editingNote._id, title, content);
        onEditComplete();
      } else {
        const newNote = await notesAPI.create(title, content);
        onNoteAdded(newNote);
      }
      setTitle('');
      setContent('');
    } catch (err) {
      setError(err.message || '操作失敗，請重試');
    } finally {
      setIsLoading(false);
    }
  };
  // ...
}
```

這裡學到的重點：用 `isLoading` 防止使用者重複點擊，是個很實用的小細節。

### 步驟 4: NoteList 元件

列表元件負責顯示所有筆記、並提供編輯和刪除入口：

```javascript
export default function NoteList({ notes, onNoteDeleted, onNoteEdit }) {
  const handleDelete = async (id) => {
    if (confirm('確定要刪除這筆筆記嗎？')) {
      await notesAPI.delete(id);
      onNoteDeleted(id);
    }
  };

  return (
    <div className="notes-grid">
      {notes.map((note) => (
        <div key={note._id} className="note-card">
          <h3>{note.title}</h3>
          <p>{note.content}</p>
          <button onClick={() => onNoteEdit(note)}>✏️ 編輯</button>
          <button onClick={() => handleDelete(note._id)}>🗑️ 刪除</button>
          <small>{new Date(note.createdAt).toLocaleDateString('zh-TW')}</small>
        </div>
      ))}
    </div>
  );
}
```

### 步驟 5: App.jsx 主狀態管理

主元件用 `useEffect` 在掛載時載入資料，並統一管理所有狀態：

```javascript
function App() {
  const [notes, setNotes] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [editingNote, setEditingNote] = useState(null);

  useEffect(() => {
    loadNotes();
  }, []); // 空陣列 = 只執行一次

  const loadNotes = async () => {
    const data = await notesAPI.getAll();
    setNotes(data);
  };

  const handleNoteAdded = (newNote) => {
    setNotes([newNote, ...notes]); // 新筆記放最前面
  };

  const handleEditComplete = async () => {
    setEditingNote(null);
    await loadNotes(); // 重新同步列表
  };
  // ...
}
```

### 步驟 6: CSS 響應式設計

我用 CSS Grid 的 `auto-fill` 做響應式，不需要寫任何 media query 就能自動適配欄數：

```css
.notes-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
}

.note-card {
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border-left: 4px solid #667eea;
  transition: transform 0.3s ease;
}

.note-card:hover {
  transform: translateY(-4px);
}
```

## 卡關紀錄

### CORS 錯誤

前端發請求時，瀏覽器 console 噴了 CORS 錯誤。原因是後端沒有設定允許跨域。

解法：在 Express 後端加一行：

```javascript
const cors = require('cors');
app.use(cors());
```

### `useEffect` 執行兩次

API 被呼叫了兩次，以為是 bug。AI 夥伴說這是 React 18 嚴格模式（Strict Mode）的預期行為，開發模式才會這樣，production 不會。

這裡有兩個概念值得記下來：

**Side effect（副作用）**：在 React 中，指的是「元件 render 以外、會影響外部的操作」，例如 API 呼叫、計時器、訂閱、操作 DOM 等。`useEffect` 就是專門用來處理這些事的 Hook。

**Idempotent（冪等）**：不管執行幾次，結果都一樣。對 API 來說，`GET /notes` 呼叫一次或兩次，回傳的資料是一樣的——這就是冪等。

React 18 Strict Mode 刻意把 `useEffect` 執行兩次，就是在問：「你的 side effect 是冪等的嗎？呼叫兩次會不會出問題？」如果不會，代表這個 effect 是安全的。

所以這不是 bug，反而是 React 幫你做的健康檢查。

| 術語 | 原文 | 意思 |
|------|------|------|
| 副作用 | side effect | render 以外影響外部的操作（API、timer、DOM...） |
| 冪等 | idempotent | 執行幾次結果都一樣 |
| Strict Mode 執行兩次 | — | React 在驗證你的 side effect 是否安全 |

### 編輯後列表沒更新

編輯完筆記，列表還是顯示舊內容。原因是只更新了後端，沒重新載入前端。

解法：`handleEditComplete` 裡呼叫 `loadNotes()` 重新同步。

## 完整資料流：新增一筆筆記

```
使用者輸入標題、內容
  ↓
點擊「新增」
  ↓
handleSubmit 驗證資料
  ↓
notesAPI.create(title, content)
  ↓
POST /api/notes → Express 後端
  ↓
儲存到 MongoDB
  ↓
回傳新筆記 JSON
  ↓
handleNoteAdded(newNote)
  ↓
setNotes([newNote, ...notes])
  ↓
React 重新渲染，新筆記出現在列表最前面
```

## 實作成果

✅ 新增筆記：表單驗證、API 呼叫、UI 即時更新  
✅ 編輯筆記：表單預填舊資料、儲存後列表同步  
✅ 刪除筆記：確認機制、即時從列表移除  
✅ 響應式：桌面多欄、手機單欄，自動適配  
✅ 錯誤處理：伺服器錯誤、網路錯誤都有提示  

## 心得

做完這個專案，我對前後端分離的理解從「概念」變成了「感覺」。

後端負責資料邏輯，前端負責呈現互動，它們透過 API 溝通，互不干涉。這個分工讓代碼變得清晰，也讓「修改一個部分不會弄壞另一個部分」成為可能。

AI 學習的旅程，繼續走下去！

---

## 專案檔案結構

```
frontend/
├── src/
│   ├── components/
│   │   ├── NoteForm.jsx
│   │   ├── NoteForm.css
│   │   ├── NoteList.jsx
│   │   └── NoteList.css
│   ├── services/
│   │   └── api.js
│   ├── App.jsx
│   ├── App.css
│   └── main.jsx
├── package.json
└── vite.config.js
```
