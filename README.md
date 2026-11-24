<!DOCTYPE html>
<html lang="ps" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>د فارمینانو حساب - SULTAN Expense Manager</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f3f5f8;
      margin: 0;
      padding: 16px;
      color: #1f2937;
    }
    .container {
      max-width: 720px;
      margin: auto;
      background: #ffffff;
      border-radius: 12px;
      padding: 18px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.06);
    }
    .login-box {
      max-width: 360px;
      margin: 60px auto;
      background: #ffffff;
      border-radius: 12px;
      padding: 24px;
      text-align: center;
      box-shadow: 0 2px 12px rgba(0,0,0,0.08);
    }
    .login-box h2 {
      margin: 0 0 8px 0;
      font-size: 20px;
      color: #0f172a;
    }
    .login-box p {
      font-size: 13px;
      color: #64748b;
      margin-bottom: 16px;
    }
    .error-msg {
      color: #b91c1c;
      font-size: 13px;
      margin-top: 8px;
      display: none;
    }
    .top-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 6px;
    }
    h1 {
      margin: 0;
      font-size: 20px;
      color: #0f172a;
    }
    .lock-btn {
      background: #e2e8f0;
      color: #1e293b;
      padding: 6px 12px;
      font-size: 13px;
      width: auto;
    }
    .lock-btn:hover {
      background: #cbd5e1;
    }
    .subtitle {
      font-size: 13px;
      color: #64748b;
      margin-bottom: 16px;
    }
    .grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
      margin-bottom: 12px;
    }
    input, select, button {
      width: 100%;
      padding: 10px;
      border-radius: 8px;
      border: 1px solid #cbd5e1;
      font-size: 14px;
      box-sizing: border-box;
    }
    button {
      background: #1d4ed8;
      color: white;
      border: none;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover {
      background: #1e40af;
    }
    .summary {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
      margin: 16px 0;
    }
    .card {
      background: #f8fafc;
      border: 1px solid #e2e8f0;
      border-radius: 10px;
      padding: 10px;
      text-align: center;
    }
    .card span {
      display: block;
      font-size: 12px;
      color: #64748b;
    }
    .card strong {
      font-size: 16px;
      margin-top: 4px;
      display: block;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
      font-size: 14px;
    }
    th, td {
      border-bottom: 1px solid #e2e8f0;
      padding: 9px 6px;
      text-align: right;
    }
    th {
      background: #f1f5f9;
      font-weight: bold;
    }
    .pos { color: #15803d; font-weight: bold; }
    .neg { color: #b91c1c; font-weight: bold; }
    .del-btn {
      background: #fee2e2;
      color: #991b1b;
      padding: 5px 8px;
      border-radius: 6px;
      font-size: 12px;
      width: auto;
    }
  </style>
</head>
<body>

  <!-- د ننوتلو برخه (PIN Screen) -->
  <div id="loginScreen" class="login-box">
    <h2>د حسابونو قفل</h2>
    <p>د فارمینانو حساب کتلو لپاره خپل پټ کوډ (PIN) ولیکئ</p>
    <input type="password" id="pinInput" inputmode="numeric" placeholder="پټ کوډ ولیکئ" style="text-align:center; margin-bottom:10px; font-size:18px;" />
    <button onclick="checkPin()">ننوتل</button>
    <div id="pinError" class="error-msg">پټ کوډ غلط دی، بیا هڅه وکړئ.</div>
  </div>

  <!-- اصلي حساب پاڼه -->
  <div id="appScreen" class="container" style="display:none;">
    <div class="top-bar">
      <h1>د فارمینانو حساب</h1>
      <button class="lock-btn" onclick="lockApp()">قفل کول</button>
    </div>
    <div class="subtitle">ورکړل شوې پیسې، لګښتونه او پاتې حساب</div>

    <div class="grid">
      <input type="text" id="foremanName" placeholder="د فارمین نوم (مثال: فارمین احمد)" />
      <select id="entryType">
        <option value="advance">ورکړل شوې پیسې (Advance)</option>
        <option value="expense">مصرف / لګښت (Expense)</option>
      </select>
    </div>

    <div class="grid">
      <input type="number" id="amount" placeholder="مبلغ (مثال: 1000)" step="any" />
      <input type="text" id="note" placeholder="تفصیل یا یادښت (اختیاري)" />
    </div>

    <button onclick="addRecord()">ثبت کړئ</button>

    <div class="summary">
      <div class="card">
        <span>ټولې ورکړل شوې</span>
        <strong id="totalAdvance">0</strong>
      </div>
      <div class="card">
        <span>ټول لګښتونه</span>
        <strong id="totalExpense">0</strong>
      </div>
      <div class="card">
        <span>پاتې مجموعي حساب</span>
        <strong id="totalBalance">0</strong>
      </div>
    </div>

    <table>
      <thead>
        <tr>
          <th>فارمین</th>
          <th>ورکړل شوې</th>
          <th>لګښت</th>
          <th>پاتې حساب</th>
          <th>حذف</th>
        </tr>
      </thead>
      <tbody id="foremanTable"></tbody>
    </table>
  </div>

  <script>
    const SECRET_PIN = "4344";

    function checkPin() {
      const entered = document.getElementById("pinInput").value.trim();
      const errorEl = document.getElementById("pinError");

      if (entered === SECRET_PIN) {
        errorEl.style.display = "none";
        document.getElementById("pinInput").value = "";
        document.getElementById("loginScreen").style.display = "none";
        document.getElementById("appScreen").style.display = "block";
        render();
      } else {
        errorEl.style.display = "block";
      }
    }

    function lockApp() {
      document.getElementById("appScreen").style.display = "none";
      document.getElementById("loginScreen").style.display = "block";
    }

    document.getElementById("pinInput").addEventListener("keypress", function(e) {
      if (e.key === "Enter") checkPin();
    });

    let records = JSON.parse(localStorage.getItem("foreman_records_v1") || "[]");

    function saveAndRender() {
      localStorage.setItem("foreman_records_v1", JSON.stringify(records));
      render();
    }

    function addRecord() {
      const name = document.getElementById("foremanName").value.trim();
      const type = document.getElementById("entryType").value;
      const amount = parseFloat(document.getElementById("amount").value);
      const note = document.getElementById("note").value.trim();

      if (!name || isNaN(amount) || amount <= 0) {
        alert("مهرباني وکړئ د فارمین نوم او سم مبلغ ولیکئ.");
        return;
      }

      records.push({
        id: Date.now(),
        name,
        type,
        amount,
        note,
        date: new Date().toLocaleDateString()
      });

      document.getElementById("amount").value = "";
      document.getElementById("note").value = "";
      saveAndRender();
    }

    function deleteForeman(name) {
      records = records.filter(r => r.name !== name);
      saveAndRender();
    }

    function render() {
      const map = {};
      let sumAdv = 0;
      let sumExp = 0;

      records.forEach(r => {
        if (!map[r.name]) {
          map[r.name] = { advance: 0, expense: 0 };
        }
        if (r.type === "advance") {
          map[r.name].advance += r.amount;
          sumAdv += r.amount;
        } else {
          map[r.name].expense += r.amount;
          sumExp += r.amount;
        }
      });

      document.getElementById("totalAdvance").innerText = sumAdv.toLocaleString();
      document.getElementById("totalExpense").innerText = sumExp.toLocaleString();
      const netTotal = sumAdv - sumExp;
      const balEl = document.getElementById("totalBalance");
      balEl.innerText = netTotal.toLocaleString();
      balEl.className = netTotal >= 0 ? "pos" : "neg";

      const tbody = document.getElementById("foremanTable");
      tbody.innerHTML = "";

      Object.keys(map).forEach(name => {
        const adv = map[name].advance;
        const exp = map[name].expense;
        const bal = adv - exp;

        const tr = document.createElement("tr");
        tr.innerHTML = `
          <td><strong>${name}</strong></td>
          <td>${adv.toLocaleString()}</td>
          <td>${exp.toLocaleString()}</td>
          <td class="${bal >= 0 ? 'pos' : 'neg'}">${bal.toLocaleString()}</td>
          <td><button class="del-btn" onclick="deleteForeman('${name.replace(/'/g, "\\'")}')">پاک</button></td>
        `;
        tbody.appendChild(tr);
      });
    }
  </script>
</body>
</html>
