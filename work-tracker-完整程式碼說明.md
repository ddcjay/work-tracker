# 📘 Work Tracker 甘特圖系統：完整程式碼說明

---

## 🔧 專案結構

```
work-tracker/
├── index.html     # 主頁：包含甘特圖顯示與任務新增表單
├── style.css      # 樣式表：設計頁面排版與風格
├── app.js         # 程式碼：控制互動邏輯與 Firebase 資料串接
└── README.md      # 說明文件：使用方法與部署方式
```

---

## 📄 index.html

```html
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>互動式甘特圖專案</title>
  <link rel="stylesheet" href="style.css">
  <link rel="stylesheet" href="https://unpkg.com/frappe-gantt/dist/frappe-gantt.css">
</head>
<body>
  <header>
    <h1>掌握每一天，成就更高效的自己</h1>
  </header>

  <section id="gantt">
    <h2>甘特圖</h2>
    <div id="gantt-container"></div>
    <h3>新增任務</h3>
    <form id="task-form">
      <input type="text" id="task-name" placeholder="任務名稱" required>
      <input type="date" id="task-start" required>
      <input type="date" id="task-end" required>
      <input type="number" id="task-progress" placeholder="進度（0~100）" min="0" max="100" required>
      <input type="text" id="task-dependency" placeholder="依賴任務 ID（可留空）">
      <button type="submit">新增任務</button>
    </form>
  </section>

  <footer>
    <p>Powered by Your Team</p>
  </footer>

  <script src="https://unpkg.com/frappe-gantt/dist/frappe-gantt.min.js"></script>
  <script type="module" src="app.js"></script>
</body>
</html>
```

---

## 🎨 style.css

```css
body {
  font-family: 'Poppins', sans-serif;
  margin: 0;
  padding: 0;
  background: #f4f8fb;
  color: #333;
}
header {
  background: #007BFF;
  color: white;
  padding: 1em;
  text-align: center;
}
section {
  margin: 2em;
  padding: 1em;
  background: white;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}
form {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 0.5em;
  margin-top: 1em;
}
input, button {
  padding: 0.5em;
  border: 1px solid #ccc;
  border-radius: 4px;
}
footer {
  text-align: center;
  padding: 1em;
  background: #007BFF;
  color: white;
}
```

---

## 🧠 app.js（使用 Firebase 模組）

```javascript
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.9.0/firebase-app.js";
import { getFirestore, collection, addDoc, onSnapshot, doc, updateDoc } from "https://www.gstatic.com/firebasejs/10.9.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const tasksRef = collection(db, "tasks");

function renderTasks(snapshotDocs) {
  const tasks = snapshotDocs.map(doc => ({ id: doc.id, ...doc.data() }));
  new Gantt("#gantt-container", tasks, {
    on_click: task => alert(task.name),
    on_date_change: async (task, start, end) => {
      await updateTask(task.id, { start: start.toISOString(), end: end.toISOString() });
    },
    on_progress_change: async (task, progress) => {
      await updateTask(task.id, { progress });
    },
    view_mode: "Day"
  });
}

onSnapshot(tasksRef, snapshot => {
  const docs = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
  renderTasks(docs);
});

document.getElementById('task-form').addEventListener('submit', async (e) => {
  e.preventDefault();
  const name = document.getElementById('task-name').value;
  const start = document.getElementById('task-start').value;
  const end = document.getElementById('task-end').value;
  const progress = parseInt(document.getElementById('task-progress').value, 10);
  const dependency = document.getElementById('task-dependency').value;
  await addDoc(tasksRef, { name, start, end, progress, dependencies: dependency });
  e.target.reset();
});

async function updateTask(id, updateData) {
  const docRef = doc(db, "tasks", id);
  await updateDoc(docRef, updateData);
}
```

---

## 🗂️ 說明

這套系統實現功能：
- 可拖曳互動甘特圖（Frappe Gantt）
- Firebase 雲端資料儲存
- 任務動態新增、即時同步
- 支援進度與日期變更

請務必將 `app.js` 中的 Firebase 設定換成你自己的！

如需升級：登入系統、使用者權限、匯出功能，請再提出！

