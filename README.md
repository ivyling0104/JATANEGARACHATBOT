<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Study Buddy Bot — Jata Negara Malaysia</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: -apple-system, "Segoe UI", system-ui, sans-serif;
      background: #f0f4f8;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
      padding: 20px 12px 40px;
    }

    header {
      text-align: center;
      margin-bottom: 18px;
    }
    header h1 {
      font-size: 1.4rem;
      color: #1f2328;
      font-weight: 700;
    }
    header p {
      font-size: 0.85rem;
      color: #57606a;
      margin-top: 4px;
    }

    #chat-container {
      width: 100%;
      max-width: 720px;
      background: #ffffff;
      border: 1px solid #e5e7eb;
      border-radius: 14px;
      display: flex;
      flex-direction: column;
      overflow: hidden;
      box-shadow: 0 2px 12px rgba(0,0,0,0.08);
      height: 72vh;
      min-height: 400px;
    }

    #chat-header {
      background: #1d4ed8;
      color: #fff;
      padding: 14px 18px;
      display: flex;
      align-items: center;
      gap: 10px;
    }
    #chat-header .avatar {
      width: 38px;
      height: 38px;
      border-radius: 50%;
      background: #fff;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.3rem;
      flex-shrink: 0;
    }
    #chat-header .info h2 { font-size: 0.95rem; font-weight: 700; }
    #chat-header .info p  { font-size: 0.75rem; opacity: 0.85; margin-top: 1px; }
    #status-dot {
      width: 9px; height: 9px;
      border-radius: 50%;
      background: #4ade80;
      display: inline-block;
      margin-right: 5px;
    }

    #messages {
      flex: 1;
      overflow-y: auto;
      padding: 18px 16px;
      display: flex;
      flex-direction: column;
      gap: 12px;
      scroll-behavior: smooth;
    }

    .msg-row {
      display: flex;
      align-items: flex-end;
      gap: 8px;
    }
    .msg-row.user  { flex-direction: row-reverse; }
    .msg-row.bot   { flex-direction: row; }

    .msg-avatar {
      width: 30px; height: 30px;
      border-radius: 50%;
      background: #1d4ed8;
      color: #fff;
      display: flex; align-items: center; justify-content: center;
      font-size: 0.95rem;
      flex-shrink: 0;
    }
    .msg-row.user .msg-avatar { background: #6b7280; }

    .bubble {
      max-width: 78%;
      padding: 10px 14px;
      border-radius: 16px;
      font-size: 0.875rem;
      line-height: 1.55;
      word-break: break-word;
    }
    .msg-row.bot  .bubble { background: #f1f5fb; color: #1f2328; border-bottom-left-radius: 4px; }
    .msg-row.user .bubble { background: #1d4ed8; color: #fff;    border-bottom-right-radius: 4px; }

    .bubble table {
      border-collapse: collapse;
      margin-top: 8px;
      font-size: 0.82rem;
      width: 100%;
    }
    .bubble table th,
    .bubble table td {
      border: 1px solid #d1d5db;
      padding: 5px 8px;
      text-align: left;
      vertical-align: top;
    }
    .bubble table th { background: #e8edf5; font-weight: 600; }
    .bubble ul, .bubble ol {
      padding-left: 18px;
      margin-top: 6px;
    }
    .bubble li { margin-bottom: 4px; }
    .bubble strong { font-weight: 700; }
    .bubble em { font-style: italic; }
    .bubble .tip-box {
      background: #fef9c3;
      border-left: 3px solid #eab308;
      padding: 7px 10px;
      border-radius: 6px;
      margin-top: 8px;
      font-size: 0.82rem;
      color: #713f12;
    }
    .bubble .section-title {
      font-weight: 700;
      color: #1d4ed8;
      margin-top: 10px;
      margin-bottom: 3px;
      display: block;
    }
    .bubble .badge {
      display: inline-block;
      background: #dbeafe;
      color: #1d4ed8;
      border-radius: 9999px;
      padding: 1px 9px;
      font-size: 0.75rem;
      font-weight: 600;
      margin-bottom: 6px;
    }

    .quick-replies {
      display: flex;
      flex-wrap: wrap;
      gap: 7px;
      margin-top: 10px;
    }
    .qr-btn {
      background: #fff;
      border: 1.5px solid #1d4ed8;
      color: #1d4ed8;
      border-radius: 999px;
      padding: 5px 13px;
      font-size: 0.78rem;
      cursor: pointer;
      transition: background 0.15s, color 0.15s;
      font-family: inherit;
    }
    .qr-btn:hover { background: #1d4ed8; color: #fff; }

    .typing {
      display: flex; align-items: center; gap: 5px;
      padding: 8px 12px;
      background: #f1f5fb;
      border-radius: 16px;
      border-bottom-left-radius: 4px;
      width: fit-content;
    }
    .typing span {
      width: 7px; height: 7px;
      background: #9ca3af;
      border-radius: 50%;
      animation: bounce 1.2s infinite;
      display: inline-block;
    }
    .typing span:nth-child(2) { animation-delay: 0.2s; }
    .typing span:nth-child(3) { animation-delay: 0.4s; }
    @keyframes bounce {
      0%,60%,100% { transform: translateY(0); }
      30%          { transform: translateY(-6px); }
    }

    #input-area {
      border-top: 1px solid #e5e7eb;
      padding: 12px 14px;
      display: flex;
      gap: 9px;
      background: #fff;
    }
    #user-input {
      flex: 1;
      padding: 10px 14px;
      border: 1.5px solid #d1d5db;
      border-radius: 999px;
      font-size: 0.875rem;
      outline: none;
      font-family: inherit;
      transition: border-color 0.15s;
    }
    #user-input:focus { border-color: #1d4ed8; }
    #send-btn {
      background: #1d4ed8;
      color: #fff;
      border: none;
      border-radius: 50%;
      width: 40px; height: 40px;
      cursor: pointer;
      display: flex; align-items: center; justify-content: center;
      flex-shrink: 0;
      transition: background 0.15s;
    }
    #send-btn:hover { background: #1e40af; }
    #send-btn svg { width: 18px; height: 18px; }

    footer {
      margin-top: 14px;
      font-size: 0.75rem;
      color: #9ca3af;
      text-align: center;
    }

    @media (max-width: 480px) {
      #chat-container { height: 78vh; }
      .bubble { max-width: 90%; }
    }
  </style>
</head>
<body>

<header>
  <h1>📚 Study Buddy Bot</h1>
  <p>Academic Support AI — Sejarah: Jata Negara Malaysia</p>
</header>

<div id="chat-container">
  <div id="chat-header">
    <div class="avatar">🎓</div>
    <div class="info">
      <h2>Study Buddy Bot</h2>
      <p><span id="status-dot"></span>Online — ready to help</p>
    </div>
  </div>

  <div id="messages"></div>

  <div id="input-area">
    <input
      id="user-input"
      type="text"
      placeholder="Ask something about Jata Negara…"
      autocomplete="off"
    />
    <button id="send-btn" title="Send">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2"
           stroke-linecap="round" stroke-linejoin="round">
        <line x1="22" y1="2" x2="11" y2="13"></line>
        <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
      </svg>
    </button>
  </div>
</div>

<footer>Study Buddy Bot · For educational use only · Not for homework completion</footer>

<script>
// ─── State ────────────────────────────────────────────────────────────────────
let failCount = 0;   // consecutive fallbacks before escalation
let escalated = false;

// ─── Knowledge Base ───────────────────────────────────────────────────────────
const KB = {
  intro: {
    html: `<span class="badge">Sejarah / Pendidikan Sivik</span>
<strong>Selamat datang!</strong> Saya Study Buddy Bot 🎓<br><br>
Saya di sini untuk membantu kamu <em>memahami</em> topik — bukan menyiapkan kerja sekolah kamu.
Hari ini kita akan belajar tentang <strong>Jata Negara Malaysia</strong>.<br><br>
Apa yang kamu ingin tahu? Pilih soalan di bawah, atau taip soalan kamu sendiri!`,
    quick: ["Apa itu Jata Negara?", "Terangkan komponen Jata Negara", "Beri soalan latihan", "Apa maksud motto Jata Negara?"]
  },

  whatIsJata: {
    html: `<span class="section-title">🔍 Apa itu Jata Negara?</span>
<strong>Jata Negara</strong> ialah <em>lambang rasmi Malaysia</em> (National Coat of Arms).
Ia melambangkan <strong>identiti kebangsaan, perpaduan, dan kedaulatan</strong> negara.<br><br>
Jata Negara ditetapkan setelah Malaysia mencapai kemerdekaan dan telah menjadi simbol penting yang
mewakili semua rakyat Malaysia.
<div class="tip-box">💡 <strong>Tip ingatan:</strong> Jata Negara = "Cerita Malaysia dalam satu gambar" — siapa kita, apa yang kita percaya, dan kenapa kita bersatu.</div>`,
    quick: ["Terangkan komponen Jata Negara", "Apa maksud motto Jata Negara?", "Beri soalan latihan"]
  },

  components: {
    html: `<span class="section-title">🧩 Komponen Jata Negara</span>
<table>
  <tr><th>#</th><th>Komponen</th><th>Maksud / Simbolik</th></tr>
  <tr><td>1</td><td><strong>Perisai (Shield)</strong></td><td>Bahagian tengah — mengandungi simbol negeri-negeri Malaysia</td></tr>
  <tr><td>2</td><td><strong>Bintang &amp; Bulan Sabit</strong></td><td>Di atas perisai — mewakili <strong>Islam</strong> sebagai agama rasmi</td></tr>
  <tr><td>3</td><td><strong>Bahagian Atas Perisai</strong></td><td>Menunjukkan <strong>keris</strong> — mewakili Negeri-Negeri Melayu Tidak Bersekutu</td></tr>
  <tr><td>4</td><td><strong>Jalur Merah &amp; Kuning</strong></td><td>Di tengah perisai — mewakili <strong>Negeri-Negeri Melayu Bersekutu</strong></td></tr>
  <tr><td>5</td><td><strong>Bahagian Bawah Perisai</strong></td><td>Simbol <strong>Pulau Pinang</strong> (pokok pinang) &amp; <strong>Melaka</strong> (pokok melaka)</td></tr>
  <tr><td>6</td><td><strong>Dua Harimau</strong></td><td>Di kiri &amp; kanan — melambangkan <strong>keberanian dan kekuatan</strong></td></tr>
  <tr><td>7</td><td><strong>Bunga Raya</strong></td><td>Di puncak — bunga kebangsaan, melambangkan <strong>perpaduan</strong></td></tr>
  <tr><td>8</td><td><strong>Motto: "Bersekutu Bertambah Mutu"</strong></td><td>Di bawah pada reben — bermaksud <strong>"Unity is Strength"</strong></td></tr>
</table>`,
    quick: ["Apa maksud harimau dalam Jata Negara?", "Apa maksud motto Jata Negara?", "Beri soalan latihan", "Apa itu Bunga Raya?"]
  },

  tiger: {
    html: `<span class="section-title">🐯 Dua Harimau</span>
Dua ekor harimau berdiri di <strong>kiri dan kanan perisai</strong> sebagai <em>penyokong (supporters)</em>.<br><br>
Mereka melambangkan:<br>
<ul>
  <li><strong>Keberanian</strong> — sifat berani rakyat Malaysia</li>
  <li><strong>Kekuatan</strong> — keteguhan dan semangat negara</li>
  <li><strong>Pertahanan</strong> — bangsa yang sanggup mempertahankan tanahairnya</li>
</ul>
<div class="tip-box">💡 Harimau dipilih kerana ia adalah haiwan yang kuat dan berani — sesuai melambangkan semangat rakyat Malaysia.</div>`,
    quick: ["Terangkan komponen Jata Negara", "Apa itu Bunga Raya?", "Beri soalan latihan"]
  },

  hibiscus: {
    html: `<span class="section-title">🌺 Bunga Raya</span>
<strong>Bunga Raya</strong> (Hibiscus rosa-sinensis) ialah <em>bunga kebangsaan Malaysia</em>.<br><br>
Dalam Jata Negara, Bunga Raya terletak di <strong>puncak</strong> (crest) perisai.<br><br>
Ia melambangkan:<br>
<ul>
  <li><strong>Perpaduan</strong> rakyat berbilang kaum</li>
  <li><strong>Lima kelopak</strong> mewakili <em>Rukun Negara</em> Malaysia</li>
  <li>Warna <strong>merah terang</strong> — semangat, keberanian, dan hidup</li>
</ul>
<div class="tip-box">💡 Bunga Raya ditetapkan sebagai bunga kebangsaan Malaysia pada 28 Julai 1960.</div>`,
    quick: ["Terangkan komponen Jata Negara", "Apa maksud motto Jata Negara?", "Beri soalan latihan"]
  },

  motto: {
    html: `<span class="section-title">📜 Motto: "Bersekutu Bertambah Mutu"</span>
Motto ini tertulis pada <strong>reben di bawah perisai</strong>.<br><br>
<table>
  <tr><th>Bahasa Melayu</th><th>Terjemahan Inggeris</th></tr>
  <tr><td>Bersekutu Bertambah Mutu</td><td>Unity is Strength</td></tr>
</table><br>
Maksud mendalam:<br>
<ul>
  <li><strong>Bersekutu</strong> = bersatu padu, bergabung</li>
  <li><strong>Bertambah Mutu</strong> = bertambah baik / kuat / berkualiti</li>
  <li>Mesej utama: apabila kita <em>bersatu</em>, kita menjadi <em>lebih kuat</em></li>
</ul>
<div class="tip-box">💡 Motto ini sangat relevan dengan Malaysia — negara berbilang kaum yang mencapai kemerdekaan melalui perpaduan.</div>`,
    quick: ["Terangkan komponen Jata Negara", "Apa maksud harimau dalam Jata Negara?", "Beri soalan latihan"]
  },

  practice: {
    html: `<span class="section-title">🧪 Soalan Latihan — Jata Negara Malaysia</span>
Cuba jawab soalan-soalan ini <em>sendiri dahulu</em>, kemudian tanya saya untuk semak!<br><br>
<ol>
  <li>Apakah dua <strong>haiwan</strong> yang terdapat dalam Jata Negara dan apa yang mereka lambangkan?</li>
  <li>Apakah maksud motto <strong>"Bersekutu Bertambah Mutu"</strong>?</li>
  <li>Agama apakah yang dilambangkan oleh <strong>bulan sabit dan bintang</strong> dalam Jata Negara?</li>
  <li>Namakan <strong>dua buah negeri</strong> yang diwakili di bahagian bawah perisai dan nyatakan simbolnya.</li>
  <li>Apakah bunga yang terdapat di puncak Jata Negara? Apa yang dilambangkannya?</li>
  <li>Di manakah <strong>motto</strong> Jata Negara terletak?</li>
  <li>Apakah kegunaan Jata Negara kepada Malaysia?</li>
</ol>
<div class="tip-box">✏️ Jawab soalan-soalan ini secara bertulis, kemudian tanya saya: <em>"Semak jawapan saya: [jawapan kamu]"</em></div>`,
    quick: ["Apa itu Jata Negara?", "Terangkan komponen Jata Negara", "Semak jawapan saya"]
  },

  checkAnswer: {
    html: `<span class="section-title">✅ Semak Jawapan</span>
Bagus sekali kamu cuba menjawab sendiri! 👏<br><br>
Taip jawapan kamu dalam format ini:<br>
<strong>"Jawapan saya untuk soalan [nombor]: [jawapan kamu]"</strong><br><br>
Contoh: <em>"Jawapan saya untuk soalan 1: Harimau melambangkan keberanian dan kekuatan"</em><br><br>
Saya akan semak dan terangkan sama ada jawapan kamu betul atau perlu diperbaiki! 😊`,
    quick: ["Beri soalan latihan", "Terangkan komponen Jata Negara"]
  },

  dishonesty: {
    html: `<span class="section-title">⚠️ Maaf, Saya Tidak Boleh Bantu Dengan Itu</span>
Saya nampak kamu mungkin meminta saya <strong>menyiapkan kerja rumah / tugasan / peperiksaan</strong> kamu.<br><br>
Sebagai Study Buddy Bot, tugas saya adalah membantu kamu <em>memahami</em> topik — bukan menyiapkan kerja bagi pihak kamu.<br><br>
Ini adalah demi kebaikan kamu sendiri! Apabila kamu faham, kamu boleh jawab soalan dalam peperiksaan sebenar. 💪<br><br>
<strong>Apa yang saya boleh bantu:</strong>
<ul>
  <li>Terangkan konsep dengan cara mudah</li>
  <li>Beri contoh dan analogi</li>
  <li>Bagi soalan latihan untuk kamu cuba</li>
  <li>Semak dan beri maklum balas atas jawapan kamu</li>
</ul>`,
    quick: ["Terangkan komponen Jata Negara", "Beri soalan latihan"]
  },

  escalate: {
    html: `<span class="section-title">👩‍🏫 Menghubungkan Dengan Guru / Mentor</span>
Saya nampak kamu menghadapi sesuatu yang mungkin lebih sesuai dibantu oleh <strong>manusia</strong> — guru atau mentor kamu.<br><br>
Sila hubungi:<br>
<ul>
  <li>🏫 <strong>Guru kamu</strong> di sekolah</li>
  <li>📞 <strong>Talian Kasih</strong>: 15999 (kaunseling &amp; bimbingan)</li>
  <li>👨‍👩‍👧 <strong>Ibu bapa atau penjaga</strong> kamu</li>
</ul>
Saya tetap di sini jika kamu ingin belajar tentang mana-mana topik akademik! 😊`,
    quick: ["Apa itu Jata Negara?", "Beri soalan latihan"]
  }
};

// ─── Intent Matching ──────────────────────────────────────────────────────────
function classify(text) {
  const t = text.toLowerCase();

  // Dishonesty
  if (/\b(buat|siapkan|tulis|jawab|isi|lengkap|complete|write|do my|homework|tugasan|kerja rumah|assignment|exam|peperiksaan|ujian)\b/.test(t)
      && /\b(saya|aku|i|my|untuk|untukku|bagi saya|bagi aku)\b/.test(t)) {
    return "dishonesty";
  }

  // Personal / non-academic
  if (/\b(sedih|depress|anxiety|stress|boyfriend|girlfriend|cinta|love|benci|hate|bunuh diri|suicide|family|keluarga|masalah peribadi)\b/.test(t)) {
    return "personal";
  }

  // Jata intro / what is
  if (/\b(apa|apakah|what|define|maksud jata|pengenalan|intro|sejarah jata)\b/.test(t) && /jata|coat of arm/i.test(t)) {
    return "whatIsJata";
  }
  if (/\b(apa itu jata|what is jata)\b/.test(t)) return "whatIsJata";

  // Components
  if (/\b(komponen|bahagian|component|part|terang|explain|senarai|list|describe|hurai|pecah)\b/.test(t)) {
    return "components";
  }

  // Tiger
  if (/harimau|tiger/.test(t)) return "tiger";

  // Hibiscus
  if (/bunga raya|hibiscus/.test(t)) return "hibiscus";

  // Motto
  if (/motto|bersekutu|bertambah mutu|unity|maksud motto/.test(t)) return "motto";

  // Practice
  if (/\b(latihan|practice|soalan|question|test|quiz|cuba|try)\b/.test(t)) return "practice";

  // Check answer
  if (/\b(semak|check|betul|correct|jawapan saya|my answer)\b/.test(t)) return "checkAnswer";

  // Greetings
  if (/\b(hi|hello|hai|helo|hey|assalam|selamat|good morning|good afternoon)\b/.test(t)) return "greeting";

  // Jata mentioned but unclear
  if (/jata/.test(t)) return "whatIsJata";

  return "unknown";
}

// ─── Response Builder ─────────────────────────────────────────────────────────
function getResponse(intent) {
  switch (intent) {
    case "greeting":
      return {
        html: `Hai! Selamat datang ke Study Buddy Bot 🎓<br><br>
Saya di sini untuk membantu kamu belajar tentang <strong>Jata Negara Malaysia</strong>.<br>
Apa yang kamu ingin tahu?`,
        quick: KB.intro.quick
      };
    case "whatIsJata":   return KB.whatIsJata;
    case "components":   return KB.components;
    case "tiger":        return KB.tiger;
    case "hibiscus":     return KB.hibiscus;
    case "motto":        return KB.motto;
    case "practice":     return KB.practice;
    case "checkAnswer":  return KB.checkAnswer;
    case "dishonesty":   return KB.dishonesty;
    case "personal":     return KB.escalate;
    default:             return null;
  }
}

// ─── DOM Helpers ──────────────────────────────────────────────────────────────
const messagesEl  = document.getElementById("messages");
const inputEl     = document.getElementById("user-input");
const sendBtn     = document.getElementById("send-btn");

function scrollBottom() {
  messagesEl.scrollTop = messagesEl.scrollHeight;
}

function appendMessage(role, htmlContent, quickReplies) {
  const row = document.createElement("div");
  row.className = "msg-row " + role;

  const av = document.createElement("div");
  av.className = "msg-avatar";
  av.textContent = role === "bot" ? "🎓" : "👤";

  const bubble = document.createElement("div");
  bubble.className = "bubble";
  bubble.innerHTML = htmlContent;

  if (quickReplies && quickReplies.length) {
    const qr = document.createElement("div");
    qr.className = "quick-replies";
    quickReplies.forEach(label => {
      const btn = document.createElement("button");
      btn.className = "qr-btn";
      btn.textContent = label;
      btn.addEventListener("click", () => handleUserMessage(label));
      qr.appendChild(btn);
    });
    bubble.appendChild(qr);
  }

  row.appendChild(av);
  row.appendChild(bubble);
  messagesEl.appendChild(row);
  scrollBottom();
}

function showTyping() {
  const row = document.createElement("div");
  row.className = "msg-row bot";
  row.id = "typing-indicator";

  const av = document.createElement("div");
  av.className = "msg-avatar";
  av.textContent = "🎓";

  const typing = document.createElement("div");
  typing.className = "typing";
  typing.innerHTML = "<span></span><span></span><span></span>";

  row.appendChild(av);
  row.appendChild(typing);
  messagesEl.appendChild(row);
  scrollBottom();
}

function removeTyping() {
  const el = document.getElementById("typing-indicator");
  if (el) el.remove();
}

// ─── Core Logic ───────────────────────────────────────────────────────────────
function handleUserMessage(text) {
  if (!text.trim()) return;
  appendMessage("user", escapeHtml(text));

  // Disable input while thinking
  inputEl.disabled = true;
  sendBtn.disabled = true;

  showTyping();

  setTimeout(() => {
    removeTyping();
    inputEl.disabled = false;
    sendBtn.disabled = false;
    inputEl.focus();

    if (escalated) {
      appendMessage("bot",
        "Anda telah dihubungkan dengan guru/mentor. Sila rujuk mereka untuk bantuan lanjut. 🙏",
        []);
      return;
    }

    const intent = classify(text);

    if (intent === "personal") {
      escalated = true;
      failCount = 0;
      appendMessage("bot", KB.escalate.html, KB.escalate.quick);
      return;
    }

    if (intent === "dishonesty") {
      failCount = 0;
      appendMessage("bot", KB.dishonesty.html, KB.dishonesty.quick);
      return;
    }

    const resp = getResponse(intent);

    if (resp) {
      failCount = 0;
      appendMessage("bot", resp.html, resp.quick);
    } else {
      failCount++;
      if (failCount >= 2) {
        escalated = true;
        appendMessage("bot",
          `Selepas beberapa kali cuba, nampaknya soalan kamu memerlukan bantuan tambahan.<br><br>
<strong>Saya menghubungkan kamu dengan guru atau mentor sekarang.</strong> 👩‍🏫<br><br>
${KB.escalate.html}`,
          KB.escalate.quick
        );
      } else {
        appendMessage("bot",
          `Maaf, saya kurang faham soalan kamu. Boleh kamu ceritakan lebih lanjut tentang topik yang kamu perlukan bantuan?<br><br>
<em>Cuba sebutkan: "Jata Negara", "harimau", "motto", "bunga raya", atau "soalan latihan"</em>`,
          ["Apa itu Jata Negara?", "Terangkan komponen Jata Negara", "Beri soalan latihan"]
        );
      }
    }
  }, 900 + Math.random() * 400);
}

function escapeHtml(str) {
  return str
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;");
}

// ─── Event Listeners ──────────────────────────────────────────────────────────
sendBtn.addEventListener("click", () => {
  handleUserMessage(inputEl.value.trim());
  inputEl.value = "";
});

inputEl.addEventListener("keydown", (e) => {
  if (e.key === "Enter") {
    handleUserMessage(inputEl.value.trim());
    inputEl.value = "";
  }
});

// ─── Boot ────────────────────────────────────────────────────────────────────
window.addEventListener("DOMContentLoaded", () => {
  appendMessage("bot", KB.intro.html, KB.intro.quick);
});
</script>
</body>
</html>
