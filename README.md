
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>リマインダー</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Hiragino Sans", "Yu Gothic", sans-serif;
    background: linear-gradient(135deg, #667eea, #764ba2);
    min-height: 100vh;
    min-height: 100dvh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 20px;
  }
  .app {
    background: #fff;
    width: 100%;
    max-width: 480px;
    border-radius: 20px;
    box-shadow: 0 20px 50px rgba(0,0,0,0.2);
    overflow: hidden;
  }
  header {
    background: #5a4fcf;
    color: #fff;
    padding: 24px 20px;
    text-align: center;
  }
  header h1 { font-size: 22px; }
  header p { font-size: 13px; opacity: 0.85; margin-top: 4px; }
  .input-area {
    display: flex;
    gap: 8px;
    padding: 16px 20px;
    border-bottom: 1px solid #eee;
    flex-wrap: wrap;
  }
  .input-area input[type="text"] {
    flex: 1 1 100%;
    padding: 12px;
    border: 1px solid #ddd;
    border-radius: 10px;
    font-size: 15px;
  }
  .input-area input[type="datetime-local"] {
    flex: 1 1 60%;
    min-width: 0;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 10px;
    font-size: 14px;
  }
  .input-area button {
    flex: 1 1 30%;
    background: #5a4fcf;
    color: #fff;
    border: none;
    border-radius: 10px;
    font-size: 15px;
    cursor: pointer;
    padding: 10px;
  }
  .input-area button:hover { background: #483dab; }
  ul { list-style: none; padding: 8px 20px 20px; }
  li {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 14px 12px;
    border-radius: 12px;
    background: #f7f7fb;
    margin-top: 10px;
    transition: 0.2s;
  }
  li.done { opacity: 0.5; }
  li.done .text { text-decoration: line-through; }
  li input[type="checkbox"] { width: 20px; height: 20px; flex-shrink: 0; }
  .info { flex: 1; min-width: 0; }
  .text { font-size: 15px; word-break: break-word; }
  .time { font-size: 12px; color: #888; margin-top: 2px; }
  .del {
    background: none;
    border: none;
    color: #e25555;
    font-size: 20px;
    cursor: pointer;
    flex-shrink: 0;
  }
  .empty { text-align: center; color: #aaa; padding: 30px 0; }
</style>
</head>
<body>
  <div class="app">
    <header>
      <h1>📌 リマインダー</h1>
      <p>やることと時間を登録して、大切な予定をうっかり忘れない。</p>
    </header>
    <div class="input-area">
      <input type="text" id="title" placeholder="やることを入力…">
      <input type="datetime-local" id="time">
      <button onclick="addItem()">追加</button>
    </div>
    <ul id="list"></ul>
  </div>

<script>
  let items = [];

  function render() {
    const list = document.getElementById('list');
    list.innerHTML = '';
    if (items.length === 0) {
      list.innerHTML = '<div class="empty">まだリマインダーはありません</div>';
      return;
    }
    items.forEach((item, i) => {
      const li = document.createElement('li');
      if (item.done) li.classList.add('done');
      const timeText = item.time
        ? new Date(item.time).toLocaleString('ja-JP', {month:'numeric', day:'numeric', hour:'2-digit', minute:'2-digit'})
        : '';
      li.innerHTML =
        '<input type="checkbox" ' + (item.done ? 'checked' : '') + ' onchange="toggle(' + i + ')">' +
        '<div class="info">' +
          '<div class="text">' + escapeHtml(item.title) + '</div>' +
          (timeText ? '<div class="time">🕐 ' + timeText + '</div>' : '') +
        '</div>' +
        '<button class="del" onclick="remove(' + i + ')">×</button>';
      list.appendChild(li);
    });
  }

  function addItem() {
    const title = document.getElementById('title').value.trim();
    const time = document.getElementById('time').value;
    if (!title) { return; }
    items.push({ title: title, time: time, done: false });
    items.sort(function(a, b) { return (a.time || '9999').localeCompare(b.time || '9999'); });
    document.getElementById('title').value = '';
    document.getElementById('time').value = '';
    render();
  }

  function toggle(i) { items[i].done = !items[i].done; render(); }
  function remove(i) { items.splice(i, 1); render(); }

  function escapeHtml(s) {
    return s.replace(/[&<>"']/g, function(c) {
      return {'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c];
    });
  }

  render();
</script>
</body>
</html>
