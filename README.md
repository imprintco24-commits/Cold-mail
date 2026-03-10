<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>ColdBot — Email Automation</title>
<link href="https://fonts.googleapis.com/css2?family=Instrument+Serif:ital@0;1&family=Geist+Mono:wght@400;500&family=Cabinet+Grotesk:wght@400;500;700;800&display=swap" rel="stylesheet"/>
<style>
  :root {
    --bg: #07080f;
    --surface: #0d1017;
    --surface2: #131720;
    --border: #1c2030;
    --border2: #252d40;
    --text: #e8ecf4;
    --muted: #4a5568;
    --muted2: #2d3748;
    --accent: #4f8ef7;
    --accent2: #1a3a6e;
    --green: #34d399;
    --green-bg: #0a2318;
    --green-border: #065f46;
    --amber: #fbbf24;
    --amber-bg: #1a1200;
    --pink: #f472b6;
    --pink-bg: #1a0a14;
    --red: #f87171;
    --red-bg: #1a0a0a;
    --purple: #a78bfa;
    --purple-bg: #120a2e;
    --purple-border: #4c1d95;
    --font-display: 'Cabinet Grotesk', sans-serif;
    --font-body: 'Cabinet Grotesk', sans-serif;
    --font-mono: 'Geist Mono', monospace;
    --radius: 10px;
    --radius-lg: 14px;
  }
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  html { scroll-behavior: smooth; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--font-body);
    font-size: 14px;
    line-height: 1.6;
    min-height: 100vh;
  }
  ::-webkit-scrollbar { width: 5px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--border2); border-radius: 3px; }

  /* ── Layout ── */
  #app { display: flex; flex-direction: column; min-height: 100vh; }

  /* ── Header ── */
  header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 14px 28px;
    border-bottom: 1px solid var(--border);
    background: rgba(7,8,15,0.95);
    backdrop-filter: blur(12px);
    position: sticky; top: 0; z-index: 40;
  }
  .logo { display: flex; align-items: center; gap: 11px; }
  .logo-icon {
    width: 34px; height: 34px;
    background: var(--accent);
    border-radius: 9px;
    display: flex; align-items: center; justify-content: center;
    font-size: 16px;
    box-shadow: 0 0 20px rgba(79,142,247,0.3);
  }
  .logo-text { line-height: 1.2; }
  .logo-name { font-family: var(--font-display); font-weight: 800; font-size: 17px; letter-spacing: -0.03em; }
  .logo-sub { font-size: 10px; color: var(--muted); letter-spacing: 0.1em; text-transform: uppercase; font-family: var(--font-mono); }
  .due-badge {
    display: flex; align-items: center; gap: 7px;
    background: var(--purple-bg); border: 1px solid var(--purple-border);
    border-radius: 20px; padding: 5px 13px;
    font-size: 12px; color: var(--purple); font-family: var(--font-mono);
    animation: fadeIn 0.3s ease;
  }
  .due-dot { width: 6px; height: 6px; background: var(--purple); border-radius: 50%; animation: pulse 1.8s infinite; }

  /* ── Nav ── */
  nav {
    display: flex; gap: 0; padding: 0 28px;
    border-bottom: 1px solid var(--border);
    background: var(--bg);
    overflow-x: auto;
  }
  .nav-btn {
    background: none; border: none; cursor: pointer;
    color: var(--muted); font-family: var(--font-body); font-size: 13px; font-weight: 500;
    padding: 12px 16px;
    border-bottom: 2px solid transparent;
    transition: color 0.2s, border-color 0.2s;
    white-space: nowrap;
  }
  .nav-btn:hover { color: var(--text); }
  .nav-btn.active { color: var(--accent); border-bottom-color: var(--accent); font-weight: 700; }

  /* ── Main ── */
  main { padding: 26px 28px; max-width: 1080px; width: 100%; }

  /* ── Panels ── */
  .panel { display: none; animation: slideUp 0.25s ease; }
  .panel.active { display: block; }

  /* ── Stats ── */
  .stats-grid { display: flex; gap: 12px; flex-wrap: wrap; margin-bottom: 24px; }
  .stat {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-lg); padding: 18px 20px; flex: 1; min-width: 110px;
    transition: border-color 0.2s;
  }
  .stat:hover { border-color: var(--border2); }
  .stat-icon { font-size: 20px; margin-bottom: 8px; }
  .stat-val { font-family: 'Instrument Serif', serif; font-size: 32px; line-height: 1; margin-bottom: 3px; }
  .stat-lbl { font-size: 11px; color: var(--muted); font-family: var(--font-mono); text-transform: uppercase; letter-spacing: 0.06em; }

  /* ── Cards ── */
  .card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-lg); padding: 20px; margin-bottom: 14px;
    transition: border-color 0.2s;
  }
  .card:hover { border-color: var(--border2); }
  .card-title { font-weight: 700; font-size: 14px; margin-bottom: 14px; color: var(--text); display: flex; align-items: center; gap: 8px; }
  .card-title .accent { color: var(--accent); }

  /* ── Due followups ── */
  .due-section {
    background: var(--purple-bg); border: 1px solid var(--purple-border);
    border-radius: var(--radius-lg); padding: 18px; margin-bottom: 22px;
  }
  .due-title { font-weight: 700; color: var(--purple); margin-bottom: 12px; font-size: 13px; display: flex; align-items: center; gap: 8px; }
  .due-row {
    display: flex; align-items: center; justify-content: space-between;
    background: rgba(7,4,22,0.6); border: 1px solid #2e1065;
    border-radius: 8px; padding: 10px 14px; margin-bottom: 8px; gap: 10px; flex-wrap: wrap;
  }
  .due-row:last-child { margin-bottom: 0; }

  /* ── Recipient rows ── */
  .recipient-row {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 13px 16px; margin-bottom: 8px;
    display: flex; align-items: center; gap: 12px; flex-wrap: wrap;
    transition: border-color 0.2s;
  }
  .recipient-row:hover { border-color: var(--border2); }
  .r-info { flex: 1; min-width: 160px; }
  .r-name { font-weight: 700; font-size: 14px; color: var(--text); }
  .r-email { font-size: 12px; color: var(--muted); font-family: var(--font-mono); }

  /* ── Status badges ── */
  .badge {
    border-radius: 6px; padding: 3px 10px; font-size: 11px;
    font-family: var(--font-mono); white-space: nowrap; border: 1px solid;
  }
  .badge-pending { background: #1a1f2e; color: #64748b; border-color: #2d3748; }
  .badge-sent { background: #0f2942; color: #60a5fa; border-color: #1e4080; }
  .badge-followup1 { background: #1a1030; color: var(--purple); border-color: var(--purple-border); }
  .badge-followup2 { background: var(--pink-bg); color: var(--pink); border-color: #831843; }
  .badge-replied { background: var(--green-bg); color: var(--green); border-color: var(--green-border); }
  .badge-completed { background: #111; color: #6b7280; border-color: #374151; }

  /* ── Buttons ── */
  .btn {
    border: none; border-radius: 8px; cursor: pointer;
    font-family: var(--font-body); font-weight: 600; font-size: 13px;
    padding: 8px 16px; transition: opacity 0.15s, transform 0.1s;
    white-space: nowrap;
  }
  .btn:hover { opacity: 0.85; }
  .btn:active { transform: scale(0.97); }
  .btn-primary { background: var(--accent); color: #fff; }
  .btn-secondary { background: var(--surface2); color: #94a3b8; border: 1px solid var(--border2); }
  .btn-green { background: var(--green-bg); color: var(--green); border: 1px solid var(--green-border); }
  .btn-red { background: var(--red-bg); color: var(--red); border: 1px solid #7f1d1d; }
  .btn-sm { padding: 5px 11px; font-size: 12px; }

  /* ── Forms ── */
  .form-section {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-lg); padding: 20px; margin-bottom: 18px;
    animation: slideUp 0.2s ease;
  }
  .form-title { font-weight: 700; color: var(--accent); margin-bottom: 14px; font-size: 13px; text-transform: uppercase; letter-spacing: 0.06em; font-family: var(--font-mono); }
  .form-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(160px, 1fr)); gap: 12px; margin-bottom: 14px; }
  .form-group { display: flex; flex-direction: column; gap: 5px; }
  .form-label { font-size: 11px; color: var(--muted); font-family: var(--font-mono); text-transform: uppercase; letter-spacing: 0.06em; }
  input, textarea, select {
    background: var(--bg); border: 1px solid var(--border);
    color: var(--text); border-radius: 8px; padding: 9px 12px;
    font-family: var(--font-body); font-size: 13px; width: 100%;
    outline: none; transition: border-color 0.2s, box-shadow 0.2s;
  }
  input:focus, textarea:focus, select:focus {
    border-color: var(--accent);
    box-shadow: 0 0 0 3px rgba(79,142,247,0.1);
  }
  textarea { resize: vertical; font-family: var(--font-mono); font-size: 12px; }
  .form-actions { display: flex; gap: 8px; flex-wrap: wrap; }

  /* ── Section header ── */
  .section-header {
    display: flex; justify-content: space-between; align-items: center;
    margin-bottom: 18px; flex-wrap: wrap; gap: 10px;
  }
  .section-title { font-family: 'Instrument Serif', serif; font-size: 26px; letter-spacing: -0.02em; }
  .section-actions { display: flex; gap: 8px; flex-wrap: wrap; }

  /* ── Section label ── */
  .section-label {
    font-size: 10px; color: var(--muted); font-family: var(--font-mono);
    text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 10px;
  }

  /* ── Templates ── */
  .template-preview {
    background: #040810; border-radius: 8px; padding: 12px;
    font-family: var(--font-mono); font-size: 12px; color: var(--muted);
    white-space: pre-wrap; line-height: 1.6; max-height: 140px; overflow: auto;
  }
  .placeholder-chip {
    display: inline-block; background: var(--surface2); border: 1px solid var(--border2);
    border-radius: 4px; padding: 1px 7px; font-family: var(--font-mono);
    font-size: 11px; color: var(--accent); margin: 0 2px;
  }

  /* ── Timeline info ── */
  .timeline {
    background: #040810; border-radius: 8px; padding: 14px 16px;
    margin-top: 14px; font-size: 13px; line-height: 2;
  }
  .tl-dot { display: inline-block; width: 8px; height: 8px; border-radius: 50%; margin-right: 8px; vertical-align: middle; }

  /* ── Empty state ── */
  .empty {
    text-align: center; padding: 64px 20px; color: var(--muted);
  }
  .empty-icon { font-size: 44px; margin-bottom: 14px; }
  .empty-title { font-size: 17px; color: #475569; margin-bottom: 6px; }
  .empty-sub { font-size: 13px; margin-bottom: 22px; }

  /* ── Modal ── */
  .modal-overlay {
    display: none; position: fixed; inset: 0;
    background: rgba(0,0,0,0.75); z-index: 100;
    align-items: center; justify-content: center; padding: 20px;
  }
  .modal-overlay.open { display: flex; animation: fadeIn 0.2s ease; }
  .modal {
    background: var(--surface); border: 1px solid var(--border2);
    border-radius: var(--radius-lg); padding: 26px;
    width: 100%; max-width: 420px; max-height: 80vh; overflow-y: auto;
    animation: slideUp 0.25s ease;
  }
  .modal-name { font-family: 'Instrument Serif', serif; font-size: 20px; margin-bottom: 2px; }
  .modal-email { font-size: 12px; color: var(--muted); font-family: var(--font-mono); margin-bottom: 18px; }
  .modal-log-title { font-size: 10px; font-family: var(--font-mono); color: var(--purple); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 12px; }
  .log-entry { display: flex; gap: 12px; padding: 9px 0; border-bottom: 1px solid var(--bg); }
  .log-dot { width: 7px; height: 7px; background: var(--accent); border-radius: 50%; margin-top: 5px; flex-shrink: 0; }
  .log-action { font-size: 13px; color: var(--text); }
  .log-time { font-size: 11px; color: var(--muted); font-family: var(--font-mono); }

  /* ── Toast ── */
  #toast {
    position: fixed; bottom: 22px; right: 22px; z-index: 200;
    border-radius: 10px; padding: 11px 18px;
    font-size: 13px; font-weight: 600;
    box-shadow: 0 8px 32px rgba(0,0,0,0.5);
    display: none; animation: slideUp 0.25s ease;
    max-width: 320px;
  }
  #toast.success { background: var(--green-bg); border: 1px solid var(--green-border); color: var(--green); }
  #toast.error { background: var(--red-bg); border: 1px solid #7f1d1d; color: var(--red); }

  /* ── Hint box ── */
  .hint {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 8px; padding: 11px 14px;
    font-size: 12px; color: var(--muted); line-height: 1.7;
    margin-bottom: 16px;
  }

  /* ── Animations ── */
  @keyframes slideUp { from { opacity: 0; transform: translateY(14px); } to { opacity: 1; transform: translateY(0); } }
  @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
  @keyframes pulse { 0%,100% { opacity: 1; } 50% { opacity: 0.3; } }

  /* ── Responsive ── */
  @media (max-width: 600px) {
    header, nav, main { padding-left: 16px; padding-right: 16px; }
    .stats-grid { gap: 8px; }
    .stat { min-width: 90px; padding: 14px; }
  }

  /* ── Scheduler info chip ── */
  .schedule-chip {
    display: inline-flex; align-items: center; gap: 6px;
    background: #0a1020; border: 1px solid #1e3050;
    border-radius: 6px; padding: 4px 10px;
    font-size: 11px; color: #60a5fa; font-family: var(--font-mono);
  }
</style>
</head>
<body>
<div id="app">

  <!-- Header -->
  <header>
    <div class="logo">
      <div class="logo-icon">✉</div>
      <div class="logo-text">
        <div class="logo-name">ColdBot</div>
        <div class="logo-sub">Email Automation</div>
      </div>
    </div>
    <div id="dueBadge" class="due-badge" style="display:none">
      <span class="due-dot"></span>
      <span id="dueCount"></span> follow-up(s) due
    </div>
  </header>

  <!-- Nav -->
  <nav>
    <button class="nav-btn active" onclick="switchTab('dashboard')">📊 Dashboard</button>
    <button class="nav-btn" onclick="switchTab('recipients')">👥 Recipients</button>
    <button class="nav-btn" onclick="switchTab('templates')">📝 Templates</button>
    <button class="nav-btn" onclick="switchTab('settings')">⚙️ Settings</button>
  </nav>

  <main>

    <!-- ═══════════ DASHBOARD ═══════════ -->
    <div id="panel-dashboard" class="panel active">
      <div class="stats-grid" id="statsGrid"></div>
      <div id="dueSection"></div>
      <div id="contactsList"></div>
    </div>

    <!-- ═══════════ RECIPIENTS ═══════════ -->
    <div id="panel-recipients" class="panel">
      <div class="section-header">
        <div class="section-title">Recipients</div>
        <div class="section-actions">
          <button class="btn btn-secondary" onclick="toggleBulk()">📋 Bulk Import</button>
          <button class="btn btn-primary" onclick="toggleAddForm()">+ Add Recipient</button>
        </div>
      </div>

      <!-- Add form -->
      <div id="addForm" class="form-section" style="display:none">
        <div class="form-title">New Recipient</div>
        <div class="form-grid">
          <div class="form-group">
            <label class="form-label">Full Name *</label>
            <input id="fName" placeholder="John Smith" onkeydown="if(event.key==='Enter')addRecipient()"/>
          </div>
          <div class="form-group">
            <label class="form-label">Email Address *</label>
            <input id="fEmail" type="email" placeholder="john@company.com" onkeydown="if(event.key==='Enter')addRecipient()"/>
          </div>
          <div class="form-group">
            <label class="form-label">Company</label>
            <input id="fCompany" placeholder="Acme Corp" onkeydown="if(event.key==='Enter')addRecipient()"/>
          </div>
        </div>
        <div class="form-actions">
          <button class="btn btn-primary" onclick="addRecipient()">Add Recipient</button>
          <button class="btn btn-secondary" onclick="toggleAddForm()">Cancel</button>
        </div>
      </div>

      <!-- Bulk form -->
      <div id="bulkForm" class="form-section" style="display:none">
        <div class="form-title">Bulk Import</div>
        <div class="hint">One recipient per line: <span class="placeholder-chip">Name, Email, Company</span> (company is optional)</div>
        <div class="form-group" style="margin-bottom:12px">
          <textarea id="bulkText" rows="5" placeholder="John Smith, john@acme.com, Acme Corp&#10;Jane Doe, jane@startup.io&#10;Bob Lee, bob@corp.com, Corp Ltd"></textarea>
        </div>
        <div class="form-actions">
          <button class="btn btn-primary" onclick="addBulk()">Import</button>
          <button class="btn btn-secondary" onclick="toggleBulk()">Cancel</button>
        </div>
      </div>

      <div id="recipientsList"></div>
    </div>

    <!-- ═══════════ TEMPLATES ═══════════ -->
    <div id="panel-templates" class="panel">
      <div class="section-header">
        <div class="section-title">Email Templates</div>
      </div>
      <div class="hint">
        Use these placeholders for personalization:
        <span class="placeholder-chip">{{name}}</span>
        <span class="placeholder-chip">{{sender}}</span>
        <span class="placeholder-chip">{{company}}</span>
        <span class="placeholder-chip">{{body}}</span>
      </div>
      <div id="templatesList"></div>
    </div>

    <!-- ═══════════ SETTINGS ═══════════ -->
    <div id="panel-settings" class="panel">
      <div class="section-header">
        <div class="section-title">Settings</div>
      </div>

      <div class="card">
        <div class="card-title">✉ Sender Identity</div>
        <div class="form-grid">
          <div class="form-group">
            <label class="form-label">Your Name</label>
            <input id="sName" oninput="saveSettings()"/>
          </div>
          <div class="form-group">
            <label class="form-label">Your Email</label>
            <input id="sEmail" type="email" oninput="saveSettings()"/>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-title">📧 Default Email Content</div>
        <div class="form-group" style="margin-bottom:12px">
          <label class="form-label">Subject Line</label>
          <input id="sSubject" oninput="saveSettings()"/>
          <div style="font-size:11px;color:var(--muted);margin-top:4px;font-family:var(--font-mono)">Use {{name}} and {{company}} for personalization</div>
        </div>
        <div class="form-group">
          <label class="form-label">Body Paragraph (replaces {{body}})</label>
          <textarea id="sBody" rows="3" style="font-family:var(--font-body);font-size:13px" oninput="saveSettings()"></textarea>
        </div>
      </div>

      <div class="card">
        <div class="card-title">📅 Follow-up Schedule</div>
        <div class="form-grid">
          <div class="form-group">
            <label class="form-label">Follow-up 1 — days after initial</label>
            <input id="sFU1" type="number" min="1" max="60" oninput="saveSettings();renderTimeline()"/>
          </div>
          <div class="form-group">
            <label class="form-label">Follow-up 2 — days after initial</label>
            <input id="sFU2" type="number" min="1" max="60" oninput="saveSettings();renderTimeline()"/>
          </div>
        </div>
        <div class="timeline" id="timeline"></div>
      </div>

      <div class="card">
        <div class="card-title">⚡ Auto Follow-up Checker</div>
        <div style="font-size:13px;color:var(--muted);margin-bottom:14px;line-height:1.7">
          ColdBot checks for due follow-ups every time you open this page and once per hour while it's open.
          Keep this tab open to run automated checks, or visit the page daily.
        </div>
        <div style="display:flex;align-items:center;gap:10px;flex-wrap:wrap">
          <div class="schedule-chip">⟳ Last check: <span id="lastCheck">never</span></div>
          <button class="btn btn-secondary btn-sm" onclick="runFollowupCheck(true)">Run Check Now</button>
        </div>
      </div>

      <button class="btn btn-primary" onclick="showToast('Settings saved!','success')">Save Settings</button>
    </div>

  </main>
</div>

<!-- Log Modal -->
<div class="modal-overlay" id="logModal" onclick="closeModal()">
  <div class="modal" onclick="event.stopPropagation()">
    <div class="modal-name" id="modalName"></div>
    <div class="modal-email" id="modalEmail"></div>
    <div class="modal-log-title">Activity Log</div>
    <div id="modalLog"></div>
    <button class="btn btn-secondary" style="margin-top:14px;width:100%" onclick="closeModal()">Close</button>
  </div>
</div>

<!-- Toast -->
<div id="toast"></div>

<script>
// ════════════════════════════════════════════════════
//  STATE
// ════════════════════════════════════════════════════
const DEFAULT_TEMPLATES = {
  initial: `Hi {{name}},

I hope this message finds you well. I came across your work and wanted to reach out personally.

{{body}}

I'd love to connect and explore how we might collaborate. Would you have 15 minutes for a quick call this week?

Best regards,
{{sender}}`,
  followup1: `Hi {{name}},

I wanted to follow up on my previous email — just making sure it didn't get buried in your inbox!

{{body}}

Happy to answer any questions you might have.

Best,
{{sender}}`,
  followup2: `Hi {{name}},

This will be my last follow-up — I don't want to take up too much of your time.

{{body}}

If the timing is ever right, feel free to reach out directly.

Wishing you well,
{{sender}}`
};

const DEFAULT_SETTINGS = {
  senderName: "Your Name",
  senderEmail: "you@yourdomain.com",
  emailSubject: "Quick question for {{name}}",
  emailBody: "I noticed your work in [industry] and thought there might be a great opportunity for us to collaborate.",
  followup1Days: 3,
  followup2Days: 7,
};

// Use localStorage for persistence
function loadState() {
  try {
    return {
      recipients: JSON.parse(localStorage.getItem('cb_recipients') || '[]'),
      templates: JSON.parse(localStorage.getItem('cb_templates') || 'null') || { ...DEFAULT_TEMPLATES },
      settings: { ...DEFAULT_SETTINGS, ...JSON.parse(localStorage.getItem('cb_settings') || '{}') },
    };
  } catch { return { recipients: [], templates: { ...DEFAULT_TEMPLATES }, settings: { ...DEFAULT_SETTINGS } }; }
}

function saveRecipients(r) { localStorage.setItem('cb_recipients', JSON.stringify(r)); }
function saveTemplates(t) { localStorage.setItem('cb_templates', JSON.stringify(t)); }
function saveSettingsStore(s) { localStorage.setItem('cb_settings', JSON.stringify(s)); }

let state = loadState();

// ════════════════════════════════════════════════════
//  HELPERS
// ════════════════════════════════════════════════════
function uid() { return Math.random().toString(36).substr(2, 9); }

function fmtDate(iso) {
  if (!iso) return '—';
  return new Date(iso).toLocaleString('en-US', { month: 'short', day: 'numeric', year: 'numeric', hour: '2-digit', minute: '2-digit' });
}

function daysFromNow(n) {
  const d = new Date();
  d.setDate(d.getDate() + parseInt(n));
  return d.toISOString();
}

function fillTemplate(tpl, recipient) {
  const s = state.settings;
  return tpl
    .replace(/{{name}}/g, recipient.name)
    .replace(/{{sender}}/g, s.senderName || 'Your Name')
    .replace(/{{company}}/g, recipient.company || 'your company')
    .replace(/{{body}}/g, s.emailBody || '');
}

function fillSubject(recipient) {
  const sub = state.settings.emailSubject || 'Quick question for {{name}}';
  return sub.replace(/{{name}}/g, recipient.name).replace(/{{company}}/g, recipient.company || '');
}

// ════════════════════════════════════════════════════
//  TOAST
// ════════════════════════════════════════════════════
let toastTimer;
function showToast(msg, type = 'success') {
  const el = document.getElementById('toast');
  el.textContent = (type === 'success' ? '✓ ' : '⚠ ') + msg;
  el.className = type;
  el.style.display = 'block';
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => { el.style.display = 'none'; }, 3200);
}

// ════════════════════════════════════════════════════
//  TABS
// ════════════════════════════════════════════════════
function switchTab(name) {
  document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('panel-' + name).classList.add('active');
  const idx = { dashboard: 0, recipients: 1, templates: 2, settings: 3 }[name];
  document.querySelectorAll('.nav-btn')[idx].classList.add('active');
  if (name === 'dashboard') renderDashboard();
  if (name === 'recipients') renderRecipientsList();
  if (name === 'templates') renderTemplates();
  if (name === 'settings') loadSettingsForm();
}

// ════════════════════════════════════════════════════
//  DUE FOLLOWUPS
// ════════════════════════════════════════════════════
function getDueFollowups() {
  const now = new Date();
  return state.recipients.filter(r => {
    if (!['sent', 'followup1'].includes(r.status)) return false;
    if (r.status === 'sent' && r.followup1ScheduledAt && new Date(r.followup1ScheduledAt) <= now) return true;
    if (r.status === 'followup1' && r.followup2ScheduledAt && new Date(r.followup2ScheduledAt) <= now) return true;
    return false;
  });
}

// ════════════════════════════════════════════════════
//  AUTO FOLLOW-UP CHECK
// ════════════════════════════════════════════════════
function runFollowupCheck(manual = false) {
  const due = getDueFollowups();
  const el = document.getElementById('lastCheck');
  if (el) el.textContent = new Date().toLocaleTimeString();

  if (due.length === 0) {
    if (manual) showToast('No follow-ups due right now.', 'success');
    return;
  }

  // Auto-open mailto for each due followup
  due.forEach(r => {
    const fuNum = r.status === 'sent' ? 1 : 2;
    const tplKey = 'followup' + fuNum;
    const subject = fillSubject(r);
    const body = fillTemplate(state.templates[tplKey] || DEFAULT_TEMPLATES[tplKey], r);
    const mailto = `mailto:${r.email}?subject=${encodeURIComponent('Follow-up: ' + subject)}&body=${encodeURIComponent(body)}`;

    // Update state
    const newStatus = fuNum === 1 ? 'followup1' : 'followup2';
    r.status = newStatus;
    r['followup' + fuNum + 'SentAt'] = new Date().toISOString();
    r.log = [...(r.log || []), { action: `Follow-up ${fuNum} sent (auto)`, at: new Date().toISOString() }];

    // Open mail client
    window.open(mailto, '_blank');
  });

  saveRecipients(state.recipients);
  renderDashboard();
  updateDueBadge();
  showToast(`${due.length} follow-up(s) opened in your mail client!`);
}

// ════════════════════════════════════════════════════
//  HEADER BADGE
// ════════════════════════════════════════════════════
function updateDueBadge() {
  const due = getDueFollowups();
  const badge = document.getElementById('dueBadge');
  const count = document.getElementById('dueCount');
  if (due.length > 0) {
    badge.style.display = 'flex';
    count.textContent = due.length;
  } else {
    badge.style.display = 'none';
  }
}

// ════════════════════════════════════════════════════
//  DASHBOARD
// ════════════════════════════════════════════════════
function renderDashboard() {
  const r = state.recipients;
  const stats = [
    { l: 'Total', v: r.length, i: '👥', c: '#64748b' },
    { l: 'Pending', v: r.filter(x => x.status === 'pending').length, i: '⏳', c: '#f59e0b' },
    { l: 'In Progress', v: r.filter(x => ['sent','followup1','followup2'].includes(x.status)).length, i: '📤', c: '#4f8ef7' },
    { l: 'Replied', v: r.filter(x => x.status === 'replied').length, i: '✅', c: '#34d399' },
    { l: 'Completed', v: r.filter(x => x.status === 'completed').length, i: '🏁', c: '#6b7280' },
  ];

  document.getElementById('statsGrid').innerHTML = stats.map(s =>
    `<div class="stat">
      <div class="stat-icon">${s.i}</div>
      <div class="stat-val" style="color:${s.c}">${s.v}</div>
      <div class="stat-lbl">${s.l}</div>
    </div>`
  ).join('');

  // Due section
  const due = getDueFollowups();
  const dueEl = document.getElementById('dueSection');
  if (due.length > 0) {
    dueEl.innerHTML = `
      <div class="due-section">
        <div class="due-title"><span class="due-dot"></span> Action Required — ${due.length} Follow-up(s) Due</div>
        ${due.map(r => `
          <div class="due-row">
            <div>
              <span style="font-weight:700;color:var(--text)">${r.name}</span>
              <span style="color:var(--muted);font-size:12px;margin-left:8px;font-family:var(--font-mono)">${r.email}</span>
            </div>
            <div style="display:flex;gap:6px;flex-wrap:wrap">
              <button class="btn btn-primary btn-sm" onclick="sendFollowup('${r.id}',${r.status==='sent'?1:2})">Send Follow-up ${r.status==='sent'?1:2}</button>
              <button class="btn btn-green btn-sm" onclick="markReplied('${r.id}')">✓ They Replied</button>
            </div>
          </div>`).join('')}
      </div>`;
  } else {
    dueEl.innerHTML = '';
  }

  // Contacts list
  const listEl = document.getElementById('contactsList');
  if (r.length === 0) {
    listEl.innerHTML = `
      <div class="empty">
        <div class="empty-icon">📭</div>
        <div class="empty-title">No recipients yet</div>
        <div class="empty-sub">Add recipients to start your campaign</div>
        <button class="btn btn-primary" onclick="switchTab('recipients')">Add Recipients →</button>
      </div>`;
    return;
  }

  listEl.innerHTML = `<div class="section-label">All Contacts (${r.length})</div>` +
    r.map(rec => recipientRowHTML(rec, true)).join('');
}

function recipientRowHTML(rec, showActions = true) {
  const badgeClass = {
    pending: 'badge-pending', sent: 'badge-sent',
    followup1: 'badge-followup1', followup2: 'badge-followup2',
    replied: 'badge-replied', completed: 'badge-completed'
  }[rec.status] || 'badge-pending';

  const badgeLabel = {
    pending: 'Pending', sent: 'Sent', followup1: 'Follow-up 1',
    followup2: 'Follow-up 2', replied: 'Replied ✓', completed: 'Completed'
  }[rec.status] || 'Pending';

  const scheduleInfo = rec.status === 'sent' && rec.followup1ScheduledAt
    ? `<span style="font-size:11px;color:var(--muted);font-family:var(--font-mono)">FU1: ${fmtDate(rec.followup1ScheduledAt)}</span>`
    : rec.status === 'followup1' && rec.followup2ScheduledAt
    ? `<span style="font-size:11px;color:var(--muted);font-family:var(--font-mono)">FU2: ${fmtDate(rec.followup2ScheduledAt)}</span>`
    : '';

  const actions = showActions ? `
    <div style="display:flex;gap:5px;flex-wrap:wrap;align-items:center">
      ${rec.status === 'pending' ? `<button class="btn btn-primary btn-sm" onclick="sendInitial('${rec.id}')">Send Initial</button>` : ''}
      ${rec.status === 'sent' ? `<button class="btn btn-primary btn-sm" onclick="sendFollowup('${rec.id}',1)">Follow-up 1</button>` : ''}
      ${rec.status === 'followup1' ? `<button class="btn btn-primary btn-sm" onclick="sendFollowup('${rec.id}',2)">Follow-up 2</button>` : ''}
      ${rec.status === 'followup2' ? `<button class="btn btn-secondary btn-sm" onclick="markCompleted('${rec.id}')">Complete</button>` : ''}
      ${['sent','followup1','followup2'].includes(rec.status) ? `<button class="btn btn-green btn-sm" onclick="markReplied('${rec.id}')">✓ Replied</button>` : ''}
      <button class="btn btn-secondary btn-sm" onclick="openLog('${rec.id}')">Log</button>
      <button class="btn btn-red btn-sm" onclick="removeRecipient('${rec.id}')">✕</button>
    </div>` : '';

  return `
    <div class="recipient-row">
      <div class="r-info">
        <div class="r-name">${rec.name}</div>
        <div class="r-email">${rec.email}${rec.company ? ' · ' + rec.company : ''}</div>
      </div>
      <span class="badge ${badgeClass}">${badgeLabel}</span>
      ${scheduleInfo}
      ${actions}
    </div>`;
}

// ════════════════════════════════════════════════════
//  SEND ACTIONS
// ════════════════════════════════════════════════════
function sendInitial(id) {
  const rec = state.recipients.find(r => r.id === id);
  if (!rec) return;
  const subject = fillSubject(rec);
  const body = fillTemplate(state.templates.initial, rec);
  window.open(`mailto:${rec.email}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`, '_blank');

  const s = state.settings;
  rec.status = 'sent';
  rec.sentAt = new Date().toISOString();
  rec.followup1ScheduledAt = daysFromNow(s.followup1Days || 3);
  rec.followup2ScheduledAt = daysFromNow(s.followup2Days || 7);
  rec.log = [...(rec.log || []), { action: 'Initial email sent', at: new Date().toISOString() }];
  saveRecipients(state.recipients);
  renderDashboard();
  updateDueBadge();
  showToast('Email opened in your mail client!');
}

function sendFollowup(id, num) {
  const rec = state.recipients.find(r => r.id === id);
  if (!rec) return;
  const tplKey = 'followup' + num;
  const subject = 'Following up: ' + fillSubject(rec);
  const body = fillTemplate(state.templates[tplKey] || DEFAULT_TEMPLATES[tplKey], rec);
  window.open(`mailto:${rec.email}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`, '_blank');

  rec.status = num === 1 ? 'followup1' : 'followup2';
  rec['followup' + num + 'SentAt'] = new Date().toISOString();
  rec.log = [...(rec.log || []), { action: `Follow-up ${num} sent`, at: new Date().toISOString() }];
  saveRecipients(state.recipients);
  renderDashboard();
  updateDueBadge();
  showToast(`Follow-up ${num} opened in your mail client!`);
}

function markReplied(id) {
  const rec = state.recipients.find(r => r.id === id);
  if (!rec) return;
  rec.status = 'replied';
  rec.repliedAt = new Date().toISOString();
  rec.log = [...(rec.log || []), { action: 'Marked as replied — all follow-ups stopped', at: new Date().toISOString() }];
  saveRecipients(state.recipients);
  renderDashboard();
  renderRecipientsList();
  updateDueBadge();
  showToast(`${rec.name} marked as replied — no more follow-ups!`);
}

function markCompleted(id) {
  const rec = state.recipients.find(r => r.id === id);
  if (!rec) return;
  rec.status = 'completed';
  rec.log = [...(rec.log || []), { action: 'Campaign completed', at: new Date().toISOString() }];
  saveRecipients(state.recipients);
  renderDashboard();
  updateDueBadge();
  showToast('Marked as completed.');
}

// ════════════════════════════════════════════════════
//  ADD / REMOVE RECIPIENTS
// ════════════════════════════════════════════════════
let addFormOpen = false, bulkFormOpen = false;

function toggleAddForm() {
  addFormOpen = !addFormOpen;
  bulkFormOpen = false;
  document.getElementById('addForm').style.display = addFormOpen ? 'block' : 'none';
  document.getElementById('bulkForm').style.display = 'none';
  if (addFormOpen) setTimeout(() => document.getElementById('fName').focus(), 50);
}

function toggleBulk() {
  bulkFormOpen = !bulkFormOpen;
  addFormOpen = false;
  document.getElementById('bulkForm').style.display = bulkFormOpen ? 'block' : 'none';
  document.getElementById('addForm').style.display = 'none';
  if (bulkFormOpen) setTimeout(() => document.getElementById('bulkText').focus(), 50);
}

function addRecipient() {
  const name = document.getElementById('fName').value.trim();
  const email = document.getElementById('fEmail').value.trim();
  const company = document.getElementById('fCompany').value.trim();
  if (!name || !email) return showToast('Name and email are required', 'error');

  state.recipients.push({
    id: uid(), name, email, company,
    status: 'pending',
    createdAt: new Date().toISOString(),
    log: [{ action: 'Added to campaign', at: new Date().toISOString() }]
  });
  saveRecipients(state.recipients);
  document.getElementById('fName').value = '';
  document.getElementById('fEmail').value = '';
  document.getElementById('fCompany').value = '';
  toggleAddForm();
  renderRecipientsList();
  updateDueBadge();
  showToast(`${name} added to campaign!`);
}

function addBulk() {
  const lines = document.getElementById('bulkText').value.split('\n').filter(l => l.trim());
  let added = 0;
  lines.forEach(line => {
    const parts = line.split(',').map(p => p.trim());
    if (parts.length >= 2 && parts[1].includes('@')) {
      state.recipients.push({
        id: uid(), name: parts[0], email: parts[1], company: parts[2] || '',
        status: 'pending',
        createdAt: new Date().toISOString(),
        log: [{ action: 'Added via bulk import', at: new Date().toISOString() }]
      });
      added++;
    }
  });
  if (added === 0) return showToast('Format: Name, Email, Company — one per line', 'error');
  saveRecipients(state.recipients);
  document.getElementById('bulkText').value = '';
  toggleBulk();
  renderRecipientsList();
  updateDueBadge();
  showToast(`${added} recipient(s) imported!`);
}

function removeRecipient(id) {
  const rec = state.recipients.find(r => r.id === id);
  state.recipients = state.recipients.filter(r => r.id !== id);
  saveRecipients(state.recipients);
  renderDashboard();
  renderRecipientsList();
  updateDueBadge();
  showToast(`${rec?.name || 'Recipient'} removed`);
}

function renderRecipientsList() {
  const el = document.getElementById('recipientsList');
  if (!el) return;
  if (state.recipients.length === 0) {
    el.innerHTML = `<div class="empty"><div class="empty-icon">👤</div><div class="empty-title">No recipients yet</div><div class="empty-sub">Add some above to start.</div></div>`;
    return;
  }
  el.innerHTML = `<div class="section-label">${state.recipients.length} recipient(s)</div>` +
    state.recipients.map(r => recipientRowHTML(r, true)).join('');
}

// ════════════════════════════════════════════════════
//  TEMPLATES
// ════════════════════════════════════════════════════
const TEMPLATE_LABELS = {
  initial: ['📧 Initial Email', 'First contact — sent manually'],
  followup1: ['🔁 Follow-up 1', `Auto-sent after ${state.settings.followup1Days} days if no reply`],
  followup2: ['🔁 Follow-up 2', `Auto-sent after ${state.settings.followup2Days} days if no reply`],
};

function renderTemplates() {
  const el = document.getElementById('templatesList');
  el.innerHTML = ['initial','followup1','followup2'].map(key => {
    const [title, sub] = TEMPLATE_LABELS[key];
    return `
      <div class="card" id="tcard-${key}">
        <div class="card-title">${title} <span style="font-size:12px;color:var(--muted);font-weight:400;font-family:var(--font-mono)">${sub}</span></div>
        <div class="template-preview" id="tpreview-${key}">${escHtml(state.templates[key])}</div>
        <div style="display:none" id="tedit-${key}">
          <textarea id="tinput-${key}" rows="10" style="margin-top:10px">${escHtml(state.templates[key])}</textarea>
          <div class="form-actions" style="margin-top:10px">
            <button class="btn btn-primary btn-sm" onclick="saveTemplate('${key}')">Save</button>
            <button class="btn btn-secondary btn-sm" onclick="cancelTemplate('${key}')">Cancel</button>
          </div>
        </div>
        <button class="btn btn-secondary btn-sm" style="margin-top:10px" onclick="editTemplate('${key}')">Edit</button>
      </div>`;
  }).join('');
}

function editTemplate(key) {
  document.getElementById('tpreview-' + key).style.display = 'none';
  document.getElementById('tedit-' + key).style.display = 'block';
  document.querySelector(`#tcard-${key} .btn`).style.display = 'none';
}

function cancelTemplate(key) {
  document.getElementById('tpreview-' + key).style.display = 'block';
  document.getElementById('tedit-' + key).style.display = 'none';
  document.querySelector(`#tcard-${key} .btn`).style.display = 'inline-block';
  document.getElementById('tinput-' + key).value = state.templates[key];
}

function saveTemplate(key) {
  state.templates[key] = document.getElementById('tinput-' + key).value;
  saveTemplates(state.templates);
  document.getElementById('tpreview-' + key).textContent = state.templates[key];
  cancelTemplate(key);
  showToast('Template saved!');
}

// ════════════════════════════════════════════════════
//  SETTINGS
// ════════════════════════════════════════════════════
function loadSettingsForm() {
  const s = state.settings;
  document.getElementById('sName').value = s.senderName || '';
  document.getElementById('sEmail').value = s.senderEmail || '';
  document.getElementById('sSubject').value = s.emailSubject || '';
  document.getElementById('sBody').value = s.emailBody || '';
  document.getElementById('sFU1').value = s.followup1Days || 3;
  document.getElementById('sFU2').value = s.followup2Days || 7;
  renderTimeline();
}

function saveSettings() {
  state.settings = {
    senderName: document.getElementById('sName').value,
    senderEmail: document.getElementById('sEmail').value,
    emailSubject: document.getElementById('sSubject').value,
    emailBody: document.getElementById('sBody').value,
    followup1Days: parseInt(document.getElementById('sFU1').value) || 3,
    followup2Days: parseInt(document.getElementById('sFU2').value) || 7,
  };
  saveSettingsStore(state.settings);
}

function renderTimeline() {
  const fu1 = parseInt(document.getElementById('sFU1')?.value) || state.settings.followup1Days;
  const fu2 = parseInt(document.getElementById('sFU2')?.value) || state.settings.followup2Days;
  const el = document.getElementById('timeline');
  if (!el) return;
  el.innerHTML = `
    <span class="tl-dot" style="background:#4f8ef7"></span><span style="color:#60a5fa">Day 0</span> — Initial email sent<br>
    <span class="tl-dot" style="background:var(--purple)"></span><span style="color:var(--purple)">Day ${fu1}</span> — Follow-up 1 sent automatically (if no reply)<br>
    <span class="tl-dot" style="background:var(--pink)"></span><span style="color:var(--pink)">Day ${fu2}</span> — Follow-up 2 sent automatically (if no reply)<br>
    <span class="tl-dot" style="background:var(--green)"></span><span style="color:var(--green)">Any time</span> — Mark as "Replied" → stops all future follow-ups
  `;
}

// ════════════════════════════════════════════════════
//  LOG MODAL
// ════════════════════════════════════════════════════
function openLog(id) {
  const rec = state.recipients.find(r => r.id === id);
  if (!rec) return;
  document.getElementById('modalName').textContent = rec.name;
  document.getElementById('modalEmail').textContent = rec.email + (rec.company ? ' · ' + rec.company : '');
  document.getElementById('modalLog').innerHTML = (rec.log || []).map(l => `
    <div class="log-entry">
      <div class="log-dot"></div>
      <div><div class="log-action">${l.action}</div><div class="log-time">${fmtDate(l.at)}</div></div>
    </div>`).join('') || '<div style="color:var(--muted);font-size:13px">No activity yet.</div>';
  document.getElementById('logModal').classList.add('open');
}

function closeModal() {
  document.getElementById('logModal').classList.remove('open');
}

// ════════════════════════════════════════════════════
//  UTILS
// ════════════════════════════════════════════════════
function escHtml(s) {
  return (s || '').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
}

// ════════════════════════════════════════════════════
//  INIT
// ════════════════════════════════════════════════════
document.addEventListener('DOMContentLoaded', () => {
  renderDashboard();
  updateDueBadge();

  // Run check on load
  runFollowupCheck(false);

  // Check every hour while tab is open
  setInterval(() => runFollowupCheck(false), 60 * 60 * 1000);

  // Keyboard: Escape closes modal
  document.addEventListener('keydown', e => { if (e.key === 'Escape') closeModal(); });
});
</script>
</body>
</html>
