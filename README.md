<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>منصة التصديقات والعهد</title>
<style>
  * { box-sizing: border-box; font-family: 'Segoe UI', Tahoma, sans-serif; }
  body { margin: 0; background: #f4f6f9; color: #222; }
  .header { background: #1e3a8a; color: white; padding: 12px 20px; display: flex; justify-content: space-between; align-items: center; }
  .container { max-width: 1100px; margin: 20px auto; padding: 0 15px; }
  .card { background: white; border-radius: 10px; padding: 20px; box-shadow: 0 2px 8px rgba(0,0,0,.08); margin-bottom: 16px; }
  .row { display: flex; gap: 12px; flex-wrap: wrap; }
  .row > * { flex: 1; min-width: 150px; }
  input, select, textarea, button { padding: 10px; border: 1px solid #ccc; border-radius: 6px; font-size: 14px; width: 100%; }
  button { background: #1e3a8a; color: white; border: none; cursor: pointer; font-weight: bold; }
  button:hover { background: #152b66; }
  button.danger { background: #dc2626; }
  button.success { background: #16a34a; }
  button.ghost { background: #e5e7eb; color: #111; }
  table { width: 100%; border-collapse: collapse; margin-top: 10px; }
  th, td { padding: 10px; text-align: right; border-bottom: 1px solid #eee; font-size: 14px; }
  th { background: #f9fafb; }
  .badge { display: inline-block; padding: 3px 10px; border-radius: 12px; font-size: 12px; font-weight: bold; }
  .b-pending { background: #fef3c7; color: #92400e; }
  .b-approved { background: #dcfce7; color: #166534; }
  .b-rejected { background: #fee2e2; color: #991b1b; }
  .tabs { display: flex; gap: 8px; margin-bottom: 16px; }
  .tabs button { flex: 1; background: #e5e7eb; color: #111; }
  .tabs button.active { background: #1e3a8a; color: white; }
  .hidden { display: none; }
  .toast { position: fixed; top: 20px; left: 50%; transform: translateX(-50%); padding: 12px 24px; border-radius: 8px; color: white; font-weight: bold; z-index: 1000; }
  .toast.ok { background: #16a34a; }
  .toast.err { background: #dc2626; }
  .mono { font-family: monospace; }
  .muted { color: #6b7280; font-size: 12px; }
  .modal-bg { position: fixed; inset: 0; background: rgba(0,0,0,.5); display: flex; align-items: center; justify-content: center; z-index: 999; padding: 20px; }
  .modal { background: white; border-radius: 10px; padding: 24px; max-width: 600px; width: 100%; max-height: 90vh; overflow: auto; }
</style>
</head>
<body>

<div class="header">
  <h3 style="margin:0">📋 منصة التصديقات والعهد</h3>
  <div id="userBox" class="hidden">
    <span id="userName"></span>
    <button class="ghost" style="width:auto;margin-right:10px" onclick="logout()">خروج</button>
  </div>
</div>

<!-- تسجيل الدخول -->
<div id="loginView" class="container" style="max-width:400px">
  <div class="card">
    <h2>تسجيل الدخول</h2>
    <label>البريد</label>
    <input id="loginEmail" value="leader@test.com">
    <label style="display:block;margin-top:10px">كلمة المرور</label>
    <input id="loginPassword" type="password" value="123456">
    <button style="margin-top:14px" onclick="login()">دخول</button>
    <div class="muted" style="margin-top:14px;line-height:1.8">
      حسابات تجريبية (كلمة المرور 123456):<br>
      requester@test.com — طالب<br>
      leader@test.com — قائد<br>
      finance@test.com — مالية<br>
      admin@test.com — مدير
    </div>
  </div>
</div>

<!-- التطبيق -->
<div id="appView" class="container hidden">
  <div class="tabs">
    <button data-tab="dashboard" onclick="switchTab('dashboard')">🏠 لوحة التحكم</button>
    <button data-tab="certifications" onclick="switchTab('certifications')">📝 التصديقات</button>
    <button data-tab="delegations" onclick="switchTab('delegations')">💰 العهد</button>
  </div>

  <div id="tab-dashboard" class="tab-content">
    <div class="card">
      <h3>الإحصائيات</h3>
      <div class="row" id="statsBox"></div>
    </div>
    <div class="card">
      <h3>العهد</h3>
      <div id="dashDelegations"></div>
    </div>
  </div>

  <div id="tab-certifications" class="tab-content hidden">
    <div class="card">
      <h3>➕ طلب تصديق جديد</h3>
      <div class="row">
        <div>
          <label>النوع</label>
          <select id="certType" onchange="toggleCertFields()">
            <option value="EXPENSE">مصروف من عهدة</option>
            <option value="ADDITION">إضافة على عهدة</option>
            <option value="GENERAL">تصديق عام</option>
          </select>
        </div>
        <div id="certDelegWrap">
          <label>العهدة</label>
          <select id="certDeleg"></select>
        </div>
        <div id="certAmountWrap">
          <label>المبلغ</label>
          <input id="certAmount" type="number" min="1" step="0.01">
        </div>
      </div>
      <label style="display:block;margin-top:10px">الوصف</label>
      <textarea id="certDesc" rows="2"></textarea>
      <button style="margin-top:10px" onclick="createCertification()">إرسال الطلب</button>
    </div>

    <div class="card">
      <h3>قائمة التصديقات</h3>
      <div class="row" style="margin-bottom:10px">
        <select id="filterStatus" onchange="loadCertifications()">
          <option value="">الكل</option>
          <option value="PENDING">معلّق</option>
          <option value="APPROVED">موافق عليه</option>
          <option value="REJECTED">مرفوض</option>
        </select>
      </div>
      <div id="certList"></div>
    </div>
  </div>

  <div id="tab-delegations" class="tab-content hidden">
    <div class="card">
      <h3>العهد</h3>
      <div id="delegList"></div>
    </div>
  </div>
</div>

<div id="modalRoot"></div>

<script>
const API = '/api';
let state = { token: localStorage.getItem('token'), user: null };

function toast(msg, type='ok') {
  const el = document.createElement('div');
  el.className = 'toast ' + type;
  el.textContent = msg;
  document.body.appendChild(el);
  setTimeout(() => el.remove(), 3000);
}

async function api(path, opts = {}) {
  const headers = { 'Content-Type': 'application/json', ...(opts.headers || {}) };
  if (state.token) headers['Authorization'] = 'Bearer ' + state.token;
  const res = await fetch(API + path, { ...opts, headers });
  const data = await res.json().catch(() => ({}));
  if (!res.ok) throw new Error(data.error || 'خطأ');
  return data;
}

async function login() {
  try {
    const email = document.getElementById('loginEmail').value;
    const password = document.getElementById('loginPassword').value;
    const data = await api('/auth/login', { method: 'POST', body: JSON.stringify({ email, password }) });
    state.token = data.token;
    localStorage.setItem('token', data.token);
    initApp();
  } catch (e) { toast(e.message, 'err'); }
}

function logout() {
  localStorage.removeItem('token');
  location.reload();
}

async function initApp() {
  if (!state.token) return;
  try {
    state.user = await api('/auth/me');
    document.getElementById('loginView').classList.add('hidden');
    document.getElementById('appView').classList.remove('hidden');
    document.getElementById('userBox').classList.remove('hidden');
    document.getElementById('userName').textContent = `${state.user.name} (${roleLabel(state.user.role)})`;
    switchTab('dashboard');
    await loadDelegationsDropdown();
  } catch { logout(); }
}

function roleLabel(r) {
  return { REQUESTER: 'طالب', LEADER: 'قائد', FINANCE: 'مالية', ADMIN: 'مدير' }[r] || r;
}

function switchTab(name) {
  document.querySelectorAll('.tab-content').forEach(x => x.classList.add('hidden'));
  document.getElementById('tab-' + name).classList.remove('hidden');
  document.querySelectorAll('.tabs button').forEach(b => b.classList.toggle('active', b.dataset.tab === name));
  if (name === 'dashboard') loadDashboard();
  if (name === 'certifications') loadCertifications();
  if (name === 'delegations') loadDelegations();
}

function toggleCertFields() {
  const t = document.getElementById('certType').value;
  document.getElementById('certDelegWrap').style.display = t === 'GENERAL' ? 'none' : '';
  document.getElementById('certAmountWrap').style.display = t === 'GENERAL' ? 'none' : '';
}

async function loadDelegationsDropdown() {
  const list = await api('/delegations');
  const sel = document.getElementById('certDeleg');
  sel.innerHTML = list.map(d =>
    `<option value="${d.id}">${d.name} — رصيد: ${d.current_balance}</option>`).join('');
}

async function loadDashboard() {
  const [certs, delegs] = await Promise.all([api('/certifications'), api('/delegations')]);
  const pending = certs.filter(c => c.status === 'PENDING').length;
  const approved = certs.filter(c => c.status === 'APPROVED').length;
  const rejected = certs.filter(c => c.status === 'REJECTED').length;
  document.getElementById('statsBox').innerHTML = `
    <div class="card" style="margin:0;text-align:center"><h2>${pending}</h2><div class="muted">معلّق</div></div>
    <div class="card" style="margin:0;text-align:center"><h2>${approved}</h2><div class="muted">موافق</div></div>
    <div class="card" style="margin:0;text-align:center"><h2>${rejected}</h2><div class="muted">مرفوض</div></div>
    <div class="card" style="margin:0;text-align:center"><h2>${delegs.length}</h2><div class="muted">عهد</div></div>
  `;
  document.getElementById('dashDelegations').innerHTML = delegs.map(d => `
    <div style="padding:8px;border-bottom:1px solid #eee">
      <b>${d.name}</b> — ${d.holder_name}
      <span style="float:left" class="mono">${d.current_balance} / ${d.initial_amount}</span>
    </div>`).join('') || '<div class="muted">لا توجد عهد</div>';
}

async function createCertification() {
  try {
    const type = document.getElementById('certType').value;
    const body = { type, description: document.getElementById('certDesc').value };
    if (type !== 'GENERAL') {
      body.delegation_id = +document.getElementById('certDeleg').value;
      body.amount = +document.getElementById('certAmount').value;
    }
    const res = await api('/certifications', { method: 'POST', body: JSON.stringify(body) });
    toast('تم إنشاء الطلب: ' + res.request_number);
    document.getElementById('certDesc').value = '';
    document.getElementById('certAmount').value = '';
    loadCertifications();
    loadDelegationsDropdown();
  } catch (e) { toast(e.message, 'err'); }
}

async function loadCertifications() {
  const status = document.getElementById('filterStatus').value;
  const list = await api('/certifications' + (status ? `?status=${status}` : ''));
  const box = document.getElementById('certList');
  if (!list.length) { box.innerHTML = '<div class="muted">لا توجد طلبات</div>'; return; }
  box.innerHTML = `<table>
    <thead><tr><th>الرقم</th><th>النوع</th><th>المبلغ</th><th>الطالب</th><th>العهدة</th><th>الحالة</th><th>إجراء</th></tr></thead>
    <tbody>${list.map(c => `
      <tr>
        <td class="mono">${c.request_number}</td>
        <td>${certTypeLabel(c.type)}</td>
        <td class="mono">${c.amount ?? '-'}</td>
        <td>${c.requester_name}</td>
        <td>${c.delegation_name || '-'}</td>
        <td><span class="badge b-${c.status.toLowerCase()}">${statusLabel(c.status)}</span></td>
        <td><button class="ghost" style="width:auto" onclick="viewCert(${c.id})">تفاصيل</button></td>
      </tr>`).join('')}</tbody>
  </table>`;
}

function certTypeLabel(t) { return { EXPENSE: 'مصروف', ADDITION: 'إضافة', GENERAL: 'عام' }[t] || t; }
function statusLabel(s) { return { PENDING: 'معلّق', APPROVED: 'موافق', REJECTED: 'مرفوض' }[s] || s; }

async function viewCert(id) {
  const data = await api('/certifications/' + id);
  const c = data.certification;
  const canAct = c.status === 'PENDING' && (state.user.role === 'LEADER' || state.user.role === 'ADMIN');

  const logs = data.logs.map(l =>
    `<div class="muted" style="padding:4px 0">${l.created_at} — ${l.actor_name}: <b>${l.action}</b>${l.comment ? ' — ' + l.comment : ''}</div>`
  ).join('');

  const tx = data.transactions.map(t =>
    `<div class="muted" style="padding:4px 0">${t.type}: ${t.amount} (${t.balance_before} → ${t.balance_after})</div>`
  ).join('');

  const root = document.getElementById('modalRoot');
  root.innerHTML = `
    <div class="modal-bg" onclick="if(event.target===this)closeModal()">
      <div class="modal">
        <h2>${c.request_number}</h2>
        <div style="padding:12px;background:#f9fafb;border-radius:8px;margin:12px 0">
          <div>النوع: <b>${certTypeLabel(c.type)}</b></div>
          <div>المبلغ: <b class="mono">${c.amount ?? '-'}</b></div>
          <div>العهدة: <b>${c.delegation_name || '-'}</b></div>
          <div>الطالب: <b>${c.requester_name}</b></div>
          <div>الوصف: ${c.description || '-'}</div>
          <div>الحالة: <span class="badge b-${c.status.toLowerCase()}">${statusLabel(c.status)}</span></div>
          ${c.leader_comment ? `<div>تعليق القائد: ${c.leader_comment}</div>` : ''}
        </div>
        <h4>السجل</h4>
        ${logs || '<div class="muted">لا يوجد</div>'}
        ${tx ? `<h4>حركات العهدة</h4>${tx}` : ''}
        ${canAct ? `
          <div style="margin-top:16px;padding-top:16px;border-top:1px solid #eee">
            <h4>إجراء القائد</h4>
            <textarea id="leaderComment" rows="2" placeholder="تعليق (إلزامي للرفض)"></textarea>
            <div class="row" style="margin-top:10px">
              <button class="success" onclick="approveCert(${c.id})">✅ موافقة</button>
              <button class="danger" onclick="rejectCert(${c.id})">❌ رفض</button>
            </div>
          </div>` : ''}
        <button class="ghost" style="margin-top:14px" onclick="closeModal()">إغلاق</button>
      </div>
    </div>`;
}

function closeModal() { document.getElementById('modalRoot').innerHTML = ''; }

async function approveCert(id) {
  try {
    const comment = document.getElementById('leaderComment').value;
    await api(`/certifications/${id}/approve`, { method: 'POST', body: JSON.stringify({ comment }) });
    toast('تمت الموافقة');
    closeModal();
    loadCertifications();
    loadDelegationsDropdown();
  } catch (e) { toast(e.message, 'err'); }
}

async function rejectCert(id) {
  try {
    const comment = document.getElementById('leaderComment').value;
    if (!comment.trim()) return toast('سبب الرفض إلزامي', 'err');
    await api(`/certifications/${id}/reject`, { method: 'POST', body: JSON.stringify({ comment }) });
    toast('تم الرفض');
    closeModal();
    loadCertifications();
  } catch (e) { toast(e.message, 'err'); }
}

async function loadDelegations() {
  const list = await api('/delegations');
  const box = document.getElementById('delegList');
  if (!list.length) { box.innerHTML = '<div class="muted">لا توجد عهد</div>'; return; }
  box.innerHTML = `<table>
    <thead><tr><th>#</th><th>الاسم</th><th>الحامل</th><th>الأولي</th><th>الحالي</th><th>إجراء</th></tr></thead>
    <tbody>${list.map(d => `
      <tr>
        <td class="mono">${d.id}</td>
        <td>${d.name}</td>
        <td>${d.holder_name}</td>
        <td class="mono">${d.initial_amount}</td>
        <td class="mono"><b>${d.current_balance}</b></td>
        <td><button class="ghost" style="width:auto" onclick="viewDelegation(${d.id})">كشف</button></td>
      </tr>`).join('')}</tbody>
  </table>`;
}

async function viewDelegation(id) {
  const data = await api('/delegations/' + id);
  const d = data.delegation;
  const tx = data.transactions.map(t => `
    <tr>
      <td class="mono">${t.id}</td>
      <td>${t.type === 'DEDUCT' ? '🔴 منصرف' : '🟢 إضافة'}</td>
      <td class="mono">${t.amount}</td>
      <td class="mono">${t.balance_before} → ${t.balance_after}</td>
      <td>${t.request_number || '-'}</td>
      <td>${t.created_by_name}</td>
      <td class="muted">${t.created_at}</td>
    </tr>`).join('');

  document.getElementById('modalRoot').innerHTML = `
    <div class="modal-bg" onclick="if(event.target===this)closeModal()">
      <div class="modal">
        <h2>${d.name}</h2>
        <div class="muted">الحامل: ${d.holder_name}</div>
        <div style="padding:12px;background:#f9fafb;border-radius:8px;margin:12px 0">
          <div>المبلغ الأصلي: <b class="mono">${d.initial_amount}</b></div>
          <div>الرصيد الحالي: <b class="mono" style="font-size:20px">${d.current_balance}</b></div>
        </div>
        <h4>الحركات</h4>
        <table>
          <thead><tr><th>#</th><th>النوع</th><th>المبلغ</th><th>الرصيد</th><th>التصديق</th><th>بواسطة</th><th>التاريخ</th></tr></thead>
          <tbody>${tx || '<tr><td colspan="7" class="muted">لا توجد حركات</td></tr>'}</tbody>
        </table>
        <button class="ghost" style="margin-top:14px" onclick="closeModal()">إغلاق</button>
      </div>
    </div>`;
}

// تشغيل تلقائي
if (state.token) initApp();
</script>
</body>
</html>
