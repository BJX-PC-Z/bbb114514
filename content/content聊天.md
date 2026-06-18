# 终端 · 残响

一个有剧情的东西，推荐品鉴之前切换到英文输入法。

---

你收到了一条来自未知账号的好友申请。ID 显示为 `~%?,·;'$?`。接受，还是拒绝？

<div style="position: relative; width: 100%; max-width: 520px; margin: 1.5rem auto; cursor: text;" id="chatLockArea">
  <style>
    .chat-window * { margin: 0; padding: 0; box-sizing: border-box; }
    .chat-window {
      width: 100%; max-width: 520px; background: #0a0a0a; border-radius: 4px;
      box-shadow: 0 8px 30px rgba(0,0,0,0.7); display: none; flex-direction: column;
      overflow: hidden; border: 1px solid #2a2a2a; position: relative; z-index: 10;
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
    }
    .chat-header {
      background: #111; border-bottom: 1px solid #1e1e1e; padding: 12px 16px;
      display: flex; align-items: center; gap: 10px; flex-shrink: 0; min-height: 50px; z-index: 3;
    }
    .chat-header .avatar {
      width: 34px; height: 34px; background: #111; border: 1px solid #333;
      display: flex; align-items: center; justify-content: center; color: #aaa; font-size: 16px; flex-shrink: 0;
    }
    .header-info { display: flex; flex-direction: column; gap: 2px; }
    .contact-name { font-size: 14px; font-weight: 600; color: #ddd; letter-spacing: 0.5px; }
    .contact-status { font-size: 11px; color: #888; transition: color 0.3s; }
    .contact-status.online { color: #5b8c5a; }
    .contact-status.typing { color: #6b8fb5; animation: pulse 1.6s infinite; }
    @keyframes pulse { 0%,100%{opacity:0.6;} 50%{opacity:1;} }
    .residual-line {
      position: absolute; top:0; left:0; width:100%; height:1px; background:#6b2a2a;
      opacity:0; transition:opacity 0.3s; z-index:4; pointer-events:none;
    }
    .residual-line.visible { opacity:1; }
    .chat-messages {
      flex:1; overflow-y:auto; padding:16px 18px; display:flex; flex-direction:column; gap:8px;
      background:#0a0a0a; scroll-behavior:smooth; -webkit-overflow-scrolling:touch;
    }
    .chat-messages::-webkit-scrollbar { width:3px; }
    .chat-messages::-webkit-scrollbar-thumb { background:#333; border-radius:10px; }
    .message-wrapper {
      display:flex; flex-direction:column; align-items:flex-start; width:100%;
      animation:fadeIn 0.3s ease-out;
    }
    @keyframes fadeIn { from{opacity:0;transform:translateY(6px);} to{opacity:1;transform:translateY(0);} }
    .message-meta { display:flex; align-items:baseline; gap:6px; margin-bottom:2px; }
    .message-sender { font-size:12px; font-weight:600; color:#aaa; letter-spacing:0.3px; }
    .message-sender.is-self { color:#ccc; }
    .message-time { font-size:10px; color:#666; }
    .message-body {
      font-size:13.5px; line-height:1.6; color:#e0e0e0; word-break:break-word;
      white-space:pre-wrap; letter-spacing:0.3px;
    }
    .message-body.stranger-text { color:#cc3333; }
    .typing-indicator {
      display:flex; align-items:center; gap:4px; padding:4px 0; align-self:flex-start; margin-top:2px;
    }
    .typing-dots { display:flex; gap:3px; }
    .typing-dot {
      width:4px; height:4px; border-radius:50%; background:#888;
      animation:dotBounce 1.4s infinite;
    }
    .typing-dot:nth-child(2) { animation-delay:0.18s; }
    .typing-dot:nth-child(3) { animation-delay:0.36s; }
    @keyframes dotBounce { 0%,60%,100%{transform:translateY(0);opacity:0.4;} 30%{transform:translateY(-7px);opacity:1;} }
    .chat-input-area {
      border-top:1px solid #1e1e1e; padding:10px 14px 12px; background:#0a0a0a;
      flex-shrink:0; display:flex; align-items:center; gap:8px; z-index:3;
    }
    .chat-input {
      flex:1; padding:10px 14px; border:1px solid #333; border-radius:3px;
      font-size:14px; background:#1a1a1a; color:#e0e0e0; outline:none;
      caret-color:#ccc; line-height:1.5; -webkit-appearance:none; transition:border-color 0.15s;
    }
    .chat-input:focus { border-color:#555; background:#222; }
    .chat-input:disabled { background:#1a1a1a; color:#666; border-color:#222; caret-color:transparent; }
    .send-btn {
      flex-shrink:0; width:34px; height:34px; background:transparent; border:1px solid #333;
      border-radius:3px; display:flex; align-items:center; justify-content:center;
      cursor:pointer; color:#888; font-size:16px; transition:background 0.1s;
    }
    .send-btn:active { background:#1a1a1a; }
    .risk-banner {
      position:absolute; top:50px; left:0; right:0; background:#3a1a1a; color:#ffb0b0;
      font-size:12px; padding:8px 16px; display:flex; justify-content:space-between; align-items:center;
      z-index:5; transform:translateX(-100%); transition:transform 0.4s ease, opacity 0.3s;
    }
    .risk-banner.slide-in { transform:translateX(0); }
    .risk-banner.hidden { opacity:0; pointer-events:none; transform:translateX(-100%); }
    .risk-close { cursor:pointer; font-size:16px; color:#cc8888; background:none; border:none; margin-left:12px; }
    .friend-overlay {
      width:100%; background:rgba(0,0,0,0.95); display:flex; align-items:center; justify-content:center;
      padding:40px 20px; border:1px solid #333; border-radius:4px;
    }
    .friend-dialog { text-align:center; color:#ccc; }
    .friend-dialog .title { font-size:16px; margin-bottom:16px; color:#ddd; }
    .friend-dialog .user-id {
      font-size:18px; color:#e0e0e0; letter-spacing:2px; margin-bottom:24px;
      animation:textGlitch 2.5s infinite;
    }
    @keyframes textGlitch { 0%,100%{transform:translate(0);} 20%{transform:translate(-1px,1px);} 40%{transform:translate(1px,-1px);} 60%{transform:translate(-1px,-1px);} 80%{transform:translate(1px,1px);} }
    .avatar-icon {
      width:48px; height:48px; background:#111; border:1px solid #333;
      margin:0 auto 16px; display:flex; align-items:center; justify-content:center;
      font-size:24px; color:#aaa; animation:shake 3s infinite;
    }
    @keyframes shake { 0%,100%{transform:rotate(0);} 25%{transform:rotate(0.5deg);} 75%{transform:rotate(-0.5deg);} }
    .button-group { display:flex; gap:16px; justify-content:center; margin-top:24px; }
    .friend-btn {
      padding:10px 28px; border:1px solid #444; background:#111; color:#ccc;
      cursor:pointer; font-size:14px; letter-spacing:1px; transition:background 0.1s;
    }
    .friend-btn.reject { border-color:#3a3a3a; color:#999; }
    .friend-btn.reject:active { background:#222; }
    .friend-btn.accept:active { background:#1a1a1a; }
    @media (max-width:560px) {
      .chat-window { max-width:100%; border-radius:0; border:none; box-shadow:none; }
    }
  </style>

  <div class="friend-overlay" id="friendOverlay">
    <div class="friend-dialog">
      <div class="avatar-icon">?</div>
      <div class="title">新的好友申请</div>
      <div class="user-id">~%?,·;'$?</div>
      <div class="button-group">
        <button class="friend-btn reject" id="btnReject">拒绝</button>
        <button class="friend-btn accept" id="btnAccept">同意</button>
      </div>
    </div>
  </div>

  <div class="chat-window" id="chatWindow">
    <div class="chat-header">
      <div class="avatar">?</div>
      <div class="header-info">
        <span class="contact-name">~%?,·;'$?</span>
        <span class="contact-status online" id="headerStatus">在线</span>
      </div>
      <div class="residual-line" id="residualLine"></div>
    </div>
    <div class="risk-banner" id="riskBanner">
      <span>⚠ 此账号存在风险</span>
      <button class="risk-close" id="riskCloseBtn">×</button>
    </div>
    <div class="chat-messages" id="chatMessages"></div>
    <div class="chat-input-area">
      <input type="text" class="chat-input" id="chatInput" placeholder="" autocomplete="off" autocorrect="off" autocapitalize="off" spellcheck="false" enterkeyhint="send">
      <button class="send-btn" id="sendBtn">➤</button>
    </div>
  </div>
</div>

<script>
(function() {
  const lockArea = document.getElementById('chatLockArea');
  const chatMessages = document.getElementById('chatMessages');
  const chatInput = document.getElementById('chatInput');
  const sendBtn = document.getElementById('sendBtn');
  const headerStatus = document.getElementById('headerStatus');
  const riskBanner = document.getElementById('riskBanner');
  const riskCloseBtn = document.getElementById('riskCloseBtn');
  const residualLine = document.getElementById('residualLine');
  const friendOverlay = document.getElementById('friendOverlay');
  const btnReject = document.getElementById('btnReject');
  const btnAccept = document.getElementById('btnAccept');
  const chatWindow = document.getElementById('chatWindow');

  // 锁定文章页面滚动
  lockArea.addEventListener('mouseenter', () => {
    document.body.style.overflow = 'hidden';
    document.body.style.touchAction = 'none';
  });
  lockArea.addEventListener('mouseleave', () => {
    document.body.style.overflow = '';
    document.body.style.touchAction = '';
  });
  lockArea.addEventListener('touchstart', (e) => {
    document.body.style.overflow = 'hidden';
    document.body.style.touchAction = 'none';
  }, {passive: false});
  document.addEventListener('click', (e) => {
    if (!lockArea.contains(e.target)) {
      document.body.style.overflow = '';
      document.body.style.touchAction = '';
    }
  });

  const dialogueScript = [
    { sender: 'A', text: '你好。', typing: 800 },
    { sender: 'B', preset: '你是谁？' },
    { sender: 'A', text: '现在不是解释这个的时候。你现在处在危险中。', typing: 2000 },
    { sender: 'B', preset: '？莫名其妙？' },
    { sender: 'A', text: '你可以望望窗外。不过别把头伸出去。你看到了吗。那有一个人影。', typing: 2400 },
    { sender: 'B', preset: '。。。怎么回事？' },
    { sender: 'A', text: '你现在要先相信我。这一切都是因为你昨天看了那份文件导致的。你的同事警告过你不要翻阅了。现在。你需要按我说的做。', typing: 2800 },
    { sender: 'B', preset: '好' },
    { sender: 'A', text: '把你卧室的门锁上。然后。关灯。就留电脑屏幕的光。', typing: 2200 },
    { sender: 'B', preset: '我照做了' },
    { sender: 'A', text: '嗯。现在它发现你会增加一些困难了。', typing: 1800 },
    { sender: 'B', preset: '我这里有声音，什么东西在我身后' },
    { sender: 'A', text: '呼吸声吗。', typing: 1200 },
    { sender: 'B', preset: '是的！我该怎么办？' },
    { sender: 'A', text: '不要动。就看着屏幕。', typing: 1400 },
    { sender: 'B', preset: '好。。。', afterSendDelay: 3000 },
    { sender: 'A', text: '它走了吗。', typing: 1000 },
    { sender: 'B', preset: '不见了' },
    { sender: 'A', text: '打开窗户。', typing: 900 },
    { sender: 'B', preset: '为什么？？', afterSendDelay: 2000 },
    { sender: 'A', text: '不要问了。！？··-》；·马上·@~￥！你想死吗', typing: 2000 },
    { sender: 'B', preset: '可是' },
    { sender: 'A', text: '快点……！%&！%#相信我。', typing: 1800 },
    { sender: 'B', preset: '我开了' },
    { sender: 'A', text: '！@%##！%', typing: 1000, afterDeliverDelay: 2000 },
    { sender: 'A', text: '现在你可以回头了。。！@！&@', typing: 2000, isFinal: true }
  ];

  let currentIndex = 0, currentPreset = '', charIndex = 0, isWaitingForReply = false;
  let isFinished = false, typingIndicator = null, inputLocked = false, pendingTimer = null;
  let currentTime = new Date(new Date().getFullYear(), new Date().getMonth(), new Date().getDate(), 23, 12, 0);

  setTimeout(() => { friendOverlay.style.display = 'flex'; }, 3000);

  btnReject.addEventListener('click', () => { btnReject.style.background = '#222'; setTimeout(() => btnReject.style.background = '', 150); });
  btnAccept.addEventListener('click', () => {
    friendOverlay.style.display = 'none';
    chatWindow.style.display = 'flex';
    setTimeout(() => { riskBanner.classList.add('slide-in'); chatMessages.style.paddingTop = '52px'; }, 500);
    setTimeout(() => { loadRound(0); chatInput.focus(); }, 1500);
  });

  riskCloseBtn.addEventListener('click', () => {
    riskBanner.classList.add('hidden'); riskBanner.classList.remove('slide-in');
    residualLine.classList.add('visible'); chatMessages.style.paddingTop = '16px';
  });

  function loadRound(index) {
    if (index >= dialogueScript.length) { finishChat(); return; }
    currentIndex = index; const item = dialogueScript[index];
    isWaitingForReply = false; inputLocked = false;
    if (item.sender === 'B') {
      currentPreset = item.preset; charIndex = 0; chatInput.value = '';
      chatInput.disabled = false; chatInput.placeholder = ''; chatInput.focus();
      headerStatus.textContent = '在线'; headerStatus.className = 'contact-status online';
    } else {
      currentPreset = ''; chatInput.disabled = true; chatInput.placeholder = '';
      showTyping(); headerStatus.textContent = '正在输入...'; headerStatus.className = 'contact-status typing';
      setTimeout(() => {
        if (isFinished) return;
        removeTyping();
        deliverMessage(item);
      }, item.typing || 1500);
    }
  }

  function deliverMessage(item) {
    addMessage('~%?,·;\'$?', item.text, formatTime(currentTime), false);
    advanceTime(1); scrollToBottom();
    if (item.isFinal) { setTimeout(finishChat, 1500); }
    else if (item.afterDeliverDelay) { pendingTimer = setTimeout(() => { pendingTimer = null; goNext(); }, item.afterDeliverDelay); }
    else { goNext(); }
  }

  function goNext() {
    const next = currentIndex + 1;
    if (next < dialogueScript.length) loadRound(next);
    else finishChat();
  }

  sendBtn.addEventListener('click', () => {
    if (isFinished || isWaitingForReply || inputLocked) return;
    if (currentPreset && charIndex < currentPreset.length) { charIndex++; updateInput(); }
    else if (currentPreset && charIndex >= currentPreset.length) sendUser();
  });

  chatInput.addEventListener('keydown', (e) => {
    if (isFinished || isWaitingForReply || inputLocked) { e.preventDefault(); return; }
    if (['ArrowLeft','ArrowRight','ArrowUp','ArrowDown','Home','End','Tab','Backspace','Delete'].includes(e.key)) { e.preventDefault(); return; }
    if (e.key === 'Enter') { e.preventDefault(); if (currentPreset && charIndex >= currentPreset.length) sendUser(); return; }
    if (e.key.length === 1) { e.preventDefault(); if (currentPreset && charIndex < currentPreset.length) { charIndex++; updateInput(); } else if (currentPreset && charIndex >= currentPreset.length) sendUser(); }
    else { e.preventDefault(); }
  });

  chatInput.addEventListener('input', () => {
    if (isWaitingForReply || isFinished || inputLocked) { chatInput.value = ''; return; }
    const expected = currentPreset ? currentPreset.substring(0, charIndex) : '';
    if (chatInput.value !== expected) { chatInput.value = expected; chatInput.setSelectionRange(expected.length, expected.length); }
  });

  function updateInput() { const val = currentPreset.substring(0, charIndex); chatInput.value = val; chatInput.setSelectionRange(val.length, val.length); }

  function sendUser() {
    if (isWaitingForReply || isFinished || inputLocked) return;
    const text = chatInput.value.trim(); if (!text) return;
    addMessage('我', text, formatTime(currentTime), true); advanceTime(1);
    chatInput.value = ''; chatInput.disabled = true; charIndex = 0; currentPreset = ''; isWaitingForReply = true; scrollToBottom();
    const delay = dialogueScript[currentIndex].afterSendDelay || 0;
    if (delay > 0) { inputLocked = true; pendingTimer = setTimeout(() => { inputLocked = false; pendingTimer = null; goNext(); }, delay); }
    else { goNext(); }
  }

  function addMessage(sender, text, time, isSelf) {
    const w = document.createElement('div'); w.className = 'message-wrapper';
    const m = document.createElement('div'); m.className = 'message-meta';
    const s = document.createElement('span'); s.className = 'message-sender' + (isSelf ? ' is-self' : ' is-stranger'); s.textContent = sender;
    const t = document.createElement('span'); t.className = 'message-time'; t.textContent = time;
    m.appendChild(s); m.appendChild(t);
    const b = document.createElement('div'); b.className = 'message-body'; if (!isSelf) b.classList.add('stranger-text'); b.textContent = text;
    w.appendChild(m); w.appendChild(b); chatMessages.appendChild(w);
  }

  function showTyping() {
    removeTyping();
    const d = document.createElement('div'); d.className = 'typing-indicator'; d.id = 'typingIndicator';
    d.innerHTML = '<span class="typing-dots"><span class="typing-dot"></span><span class="typing-dot"></span><span class="typing-dot"></span></span>';
    chatMessages.appendChild(d); typingIndicator = d; scrollToBottom();
  }

  function removeTyping() { if (typingIndicator?.parentNode) typingIndicator.remove(); const el = document.getElementById('typingIndicator'); if (el) el.remove(); typingIndicator = null; }

  function finishChat() {
    if (isFinished) return;
    isFinished = true; removeTyping(); if (pendingTimer) clearTimeout(pendingTimer);
    chatInput.disabled = true; chatInput.value = ''; chatInput.placeholder = '';
    headerStatus.textContent = '离线'; headerStatus.className = 'contact-status'; headerStatus.style.color = '#777';
    chatWindow.style.opacity = '0';
    setTimeout(() => {
      chatWindow.style.display = 'none';
      document.body.style.overflow = '';
      document.body.style.touchAction = '';
    }, 800);
  }

  function formatTime(d) { return String(d.getHours()).padStart(2,'0') + ':' + String(d.getMinutes()).padStart(2,'0'); }
  function advanceTime(m) { currentTime = new Date(currentTime.getTime() + m * 60000); }
  function scrollToBottom() { requestAnimationFrame(() => { chatMessages.scrollTop = chatMessages.scrollHeight; }); }
})();
</script>