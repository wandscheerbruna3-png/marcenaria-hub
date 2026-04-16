// ─── Storage ───────────────────────────────────────────────────────────────
const DB_KEY = 'marcenaria_obras_v1';
const API_KEY_STORAGE = 'anthropic_api_key';

function loadObras() {
  try { return JSON.parse(localStorage.getItem(DB_KEY) || '[]'); } catch { return []; }
}

function saveObras(obras) {
  localStorage.setItem(DB_KEY, JSON.stringify(obras));
}

function getApiKey() {
  return localStorage.getItem(API_KEY_STORAGE) || '';
}

// ─── State ─────────────────────────────────────────────────────────────────
let obras = loadObras();
let pdfData = null;
let pdfFileName = '';

// ─── Navigation ────────────────────────────────────────────────────────────
function navigate(page) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('page-' + page).classList.add('active');
  document.querySelector(`[data-page="${page}"]`)?.classList.add('active');
  if (page === 'projetos') renderProjetos();
  if (page === 'integracoes') loadApiKeyField();
}

document.querySelectorAll('.nav-item').forEach(item => {
  item.addEventListener('click', e => {
    e.preventDefault();
    navigate(item.dataset.page);
  });
});

// ─── Stats ─────────────────────────────────────────────────────────────────
function updateStats() {
  document.getElementById('stat-total').textContent = obras.length;
  document.getElementById('stat-pdf').textContent = obras.filter(o => o.temPdf).length;
  const uniqueResp = new Set(obras.map(o => o.responsavel.toLowerCase().trim()));
  document.getElementById('stat-resp').textContent = uniqueResp.size;
}

// ─── Projetos ──────────────────────────────────────────────────────────────
function statusLabel(s) {
  return { em_andamento: 'Em andamento', orcamento: 'Orçamento', concluido: 'Concluído', pausado: 'Pausado' }[s] || s;
}

function initials(name) {
  return name.trim().split(/\s+/).slice(0, 2).map(w => w[0].toUpperCase()).join('');
}

function renderProjetos(filter = '') {
  updateStats();
  const grid = document.getElementById('projetos-grid');
  let list = obras.slice().reverse();
  if (filter) {
    const q = filter.toLowerCase();
    list = list.filter(o =>
      o.nome.toLowerCase().includes(q) ||
      o.responsavel.toLowerCase().includes(q) ||
      (o.resumo || '').toLowerCase().includes(q)
    );
  }
  if (!list.length) {
    grid.innerHTML = `<div class="empty-state">
      <svg width="40" height="40" fill="none" stroke="#b0a898" stroke-width="1" viewBox="0 0 24 24"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/></svg>
      <p>${filter ? 'Nenhuma obra encontrada para "' + filter + '"' : 'Nenhuma obra cadastrada ainda.'}</p>
    </div>`;
    return;
  }
  grid.innerHTML = list.map(o => `
    <div class="projeto-card">
      <div class="projeto-card-top">
        <div style="display:flex;align-items:center;gap:10px">
          <div class="projeto-avatar">${initials(o.responsavel)}</div>
          <div>
            <div class="projeto-nome">${o.nome}</div>
            <div class="projeto-resp">${o.responsavel}</div>
          </div>
        </div>
        <span class="status-badge status-${o.status}">${statusLabel(o.status)}</span>
      </div>
      ${o.resumo ? `<div class="projeto-desc">${o.resumo.slice(0, 180)}${o.resumo.length > 180 ? '…' : ''}</div>` : ''}
      <div>
        <span class="pdf-pill ${o.temPdf ? 'has' : 'no'}">
          ${o.temPdf ? '✓ PDF indexado' : '! Sem PDF'}
        </span>
        <span style="font-size:11px;color:#b0a898;margin-left:8px">${o.dataCadastro}</span>
      </div>
    </div>
  `).join('');
}

function filterObras() {
  renderProjetos(document.getElementById('filter-input').value);
}

// ─── Cadastro ──────────────────────────────────────────────────────────────
function handleFile(input) {
  const file = input.files[0];
  if (!file) return;
  pdfFileName = file.name;
  const reader = new FileReader();
  reader.onload = e => {
    pdfData = e.target.result.split(',')[1];
    const dz = document.getElementById('drop-zone');
    dz.classList.add('has-file');
    document.getElementById('drop-label').textContent = file.name + ' (' + (file.size / 1024).toFixed(0) + ' KB)';
  };
  reader.readAsDataURL(file);
}

async function salvarObra() {
  const nome = document.getElementById('f-nome').value.trim();
  const resp = document.getElementById('f-resp').value.trim();
  const tel = document.getElementById('f-tel').value.trim();
  const status = document.getElementById('f-status').value;
  const obs = document.getElementById('f-obs').value.trim();

  if (!nome || !resp) { toast('Preencha nome e responsável'); return; }

  const btn = document.getElementById('btn-salvar');
  btn.disabled = true;
  btn.textContent = pdfData ? 'Lendo PDF com IA…' : 'Salvando…';

  let resumo = obs;

  if (pdfData && getApiKey()) {
    try {
      const res = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'x-api-key': getApiKey(),
          'anthropic-version': '2023-06-01',
          'anthropic-dangerous-direct-browser-access': 'true'
        },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514',
          max_tokens: 800,
          system: 'Você é especialista em projetos de marcenaria e construção civil. Extraia as informações técnicas mais importantes do PDF em português: tipo de imóvel, materiais, ambientes, acabamentos, elementos especiais. Seja conciso. Responda apenas com o resumo, sem introdução.',
          messages: [{
            role: 'user',
            content: [
              { type: 'document', source: { type: 'base64', media_type: 'application/pdf', data: pdfData } },
              { type: 'text', text: 'Resuma as informações técnicas deste projeto de marcenaria.' }
            ]
          }]
        })
      });
      const d = await res.json();
      if (d.content?.[0]?.text) resumo = d.content[0].text;
    } catch (e) {
      console.error('Erro ao ler PDF:', e);
    }
  }

  obras.push({
    id: Date.now(),
    nome, responsavel: resp, telefone: tel,
    status, resumo, obs,
    temPdf: !!pdfData,
    pdfNome: pdfFileName,
    dataCadastro: new Date().toLocaleDateString('pt-BR')
  });
  saveObras(obras);

  // Reset form
  document.getElementById('f-nome').value = '';
  document.getElementById('f-resp').value = '';
  document.getElementById('f-tel').value = '';
  document.getElementById('f-obs').value = '';
  document.getElementById('f-status').value = 'em_andamento';
  pdfData = null; pdfFileName = '';
  document.getElementById('drop-zone').classList.remove('has-file');
  document.getElementById('drop-label').textContent = 'Clique para selecionar o PDF';
  document.getElementById('pdf-input').value = '';

  btn.disabled = false;
  btn.textContent = 'Salvar projeto';
  toast('Projeto salvo!');
  navigate('projetos');
}

// ─── Chat / Busca ──────────────────────────────────────────────────────────
function setQuery(text) {
  document.getElementById('chat-input').value = text;
  navigate('busca');
  document.getElementById('chat-input').focus();
}

function appendMsg(role, text) {
  const container = document.getElementById('chat-messages');
  const welcome = container.querySelector('.chat-welcome');
  if (welcome) welcome.remove();

  const div = document.createElement('div');
  div.className = 'msg ' + role;

  if (text === '__typing__') {
    div.innerHTML = `<div class="msg-bubble ai"><div class="typing-dots"><span></span><span></span><span></span></div></div>`;
    div.id = 'typing-indicator';
  } else {
    div.innerHTML = `<div class="msg-bubble">${text.replace(/\n/g, '<br>')}</div>`;
  }
  container.appendChild(div);
  container.scrollTop = container.scrollHeight;
  return div;
}

async function sendQuery() {
  const input = document.getElementById('chat-input');
  const q = input.value.trim();
  if (!q) return;
  input.value = '';

  appendMsg('user', q);
  appendMsg('ai', '__typing__');

  const apiKey = getApiKey();
  if (!apiKey) {
    document.getElementById('typing-indicator')?.remove();
    appendMsg('ai', 'Configure sua chave da API Anthropic na página de Integrações para usar a busca com IA.');
    return;
  }

  if (!obras.length) {
    document.getElementById('typing-indicator')?.remove();
    appendMsg('ai', 'Nenhum projeto cadastrado ainda. Vá em "Nova Obra" para começar.');
    return;
  }

  const contexto = obras.map(o =>
    `OBRA: ${o.nome}\nRESPONSÁVEL: ${o.responsavel}${o.telefone ? '\nTELEFONE: ' + o.telefone : ''}\nSTATUS: ${statusLabel(o.status)}\nDESCRIÇÃO: ${o.resumo || o.obs || 'Sem descrição'}\nCADASTRADA EM: ${o.dataCadastro}`
  ).join('\n\n---\n\n');

  try {
    const res = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': apiKey,
        'anthropic-version': '2023-06-01',
        'anthropic-dangerous-direct-browser-access': 'true'
      },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        system: 'Você é um assistente especializado em gestão de obras de marcenaria e construção civil. Responda perguntas sobre o banco de projetos de forma clara, direta e em português. Cite nomes exatos. Se não encontrar, diga claramente.',
        messages: [{ role: 'user', content: `Banco de projetos:\n\n${contexto}\n\n---\n\nPergunta: ${q}` }]
      })
    });
    const d = await res.json();
    document.getElementById('typing-indicator')?.remove();
    if (d.error) { appendMsg('ai', 'Erro da API: ' + d.error.message); return; }
    appendMsg('ai', d.content[0]?.text || 'Sem resposta.');
  } catch (e) {
    document.getElementById('typing-indicator')?.remove();
    appendMsg('ai', 'Erro de conexão. Verifique sua chave da API.');
  }
}

// ─── Integrações ───────────────────────────────────────────────────────────
function loadApiKeyField() {
  const key = getApiKey();
  const input = document.getElementById('api-key-input');
  if (input) input.value = key ? '••••••••••••••••' : '';
  const status = document.querySelector('.int-status.connected');
  if (status) status.textContent = key ? 'Conectado' : 'Não configurado';
}

function saveApiKey() {
  const val = document.getElementById('api-key-input').value.trim();
  if (!val || val.startsWith('•')) { toast('Digite uma chave válida'); return; }
  localStorage.setItem(API_KEY_STORAGE, val);
  document.getElementById('api-key-input').value = '••••••••••••••••';
  document.querySelector('.int-status.connected').textContent = 'Conectado';
  toast('Chave salva!');
}

// ─── Toast ─────────────────────────────────────────────────────────────────
function toast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2500);
}

// ─── Init ──────────────────────────────────────────────────────────────────
renderProjetos();
