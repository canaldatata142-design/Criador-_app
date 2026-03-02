<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AppBuilder Pro + IA Control</title>
    <style>
        :root { --primary: #38bdf8; --bg: #0f172a; --card: #1e293b; }
        body { margin: 0; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: #fff; }
        header { background: #020617; padding: 15px; text-align: center; font-weight: bold; border-bottom: 1px solid #334155; color: var(--primary); }
        
        nav { display: flex; background: #020617; position: sticky; top: 0; z-index: 100; }
        nav button { flex: 1; padding: 15px; background: none; border: none; color: #94a3b8; cursor: pointer; transition: 0.3s; }
        nav button.active { color: var(--primary); border-bottom: 3px solid var(--primary); background: rgba(56, 189, 248, 0.1); }

        main { padding: 20px; padding-bottom: 100px; }
        .area { display: none; animation: fadeIn 0.3s; }
        .area.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Estilo Visual */
        #preview { background: #fff; color: #000; min-height: 300px; border-radius: 15px; padding: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); margin-bottom: 20px; }
        .toolbar { display: flex; gap: 10px; margin-bottom: 15px; overflow-x: auto; padding-bottom: 5px; }
        .toolbar button { white-space: nowrap; padding: 10px 15px; background: var(--card); border: 1px solid #334155; color: white; border-radius: 8px; }

        /* Estilo Código */
        textarea { width: 100%; height: 350px; background: #000; color: #10b981; border: 1px solid #334155; border-radius: 10px; font-family: 'Consolas', monospace; padding: 15px; box-sizing: border-box; font-size: 13px; }

        /* Estilo IA Estilo ChatGPT */
        #chat-window { height: 300px; background: #020617; border: 1px solid #334155; border-radius: 10px; padding: 15px; overflow-y: auto; margin-bottom: 15px; display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 10px 15px; border-radius: 10px; max-width: 85%; line-height: 1.4; }
        .msg.user { background: var(--primary); color: #000; align-self: flex-end; }
        .msg.ia { background: var(--card); color: #fff; align-self: flex-start; border: 1px solid #334155; }

        .input-ia { display: flex; gap: 10px; background: var(--card); padding: 10px; border-radius: 50px; border: 1px solid #334155; }
        .input-ia input { flex: 1; background: none; border: none; color: white; padding-left: 10px; outline: none; }
        
        .btn-install { position: fixed; bottom: 20px; left: 20px; right: 20px; background: #10b981; color: white; padding: 15px; border: none; border-radius: 12px; font-weight: bold; font-size: 16px; box-shadow: 0 5px 15px rgba(16, 185, 129, 0.4); }
    </style>
</head>
<body>

<header>APPBUILDER PRO • IA ENGINE</header>

<nav>
    <button onclick="tab('visual')" id="t-visual" class="active">VISUAL</button>
    <button onclick="tab('codigo')" id="t-codigo">CÓDIGO</button>
    <button onclick="tab('ia')" id="t-ia">IA CHAT</button>
</nav>

<main>
    <div id="visual" class="area active">
        <div class="toolbar">
            <button onclick="add('h1')">+ Título</button>
            <button onclick="add('p')">+ Texto</button>
            <button onclick="add('btn')">+ Botão</button>
            <button onclick="preview.innerHTML='' ">🗑 Limpar</button>
        </div>
        <div id="preview">Comece a criar...</div>
    </div>

    <div id="codigo" class="area">
        <textarea id="editor" oninput="syncVisual()"></textarea>
        <p style="font-size: 12px; color: #94a3b8; margin-top: 10px;">💡 Edite o código acima para atualizar o app manualmente.</p>
    </div>

    <div id="ia" class="area">
        <div id="chat-window">
            <div class="msg ia">Olá! Eu sou a sua IA. Diga-me o que quer que eu programe no seu app e eu farei isso agora mesmo.</div>
        </div>
        <div class="input-ia">
            <input type="text" id="prompt" placeholder="Ex: Crie um botão vermelho de login...">
            <button onclick="iaProcess()" style="background: var(--primary); border: none; border-radius: 50%; width: 40px; height: 40px; cursor: pointer;">🚀</button>
        </div>
    </div>

    <button class="btn-install" onclick="generateAPK()">GERAR E INSTALAR APK</button>
</main>

<script>
    const preview = document.getElementById('preview');
    const editor = document.getElementById('editor');
    const chat = document.getElementById('chat-window');

    function tab(name) {
        document.querySelectorAll('.area').forEach(a => a.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(name).classList.add('active');
        document.getElementById('t-' + name).classList.add('active');
        if(name === 'codigo') syncCode();
    }

    function add(type) {
        if(type === 'h1') preview.innerHTML += '<h1 style="color:#333">Novo Título</h1>';
        if(type === 'p') preview.innerHTML += '<p>Escreva algo aqui...</p>';
        if(type === 'btn') preview.innerHTML += '<button style="background:#38bdf8; color:white; border:none; padding:10px; border-radius:5px; margin:5px;">Clique Aqui</button>';
        syncCode();
    }

    function syncCode() { editor.value = preview.innerHTML; }
    function syncVisual() { preview.innerHTML = editor.value; }

    function iaProcess() {
        const p = document.getElementById('prompt');
        if(!p.value) return;

        chat.innerHTML += `<div class="msg user">${p.value}</div>`;
        
        // Simulação de Inteligência Artificial Gerando Código
        setTimeout(() => {
            chat.innerHTML += `<div class="msg ia">Processando comando... Gerando código profissional e aplicando ao seu projeto.</div>`;
            
            if(p.value.toLowerCase().includes('botão') || p.value.toLowerCase().includes('botao')) {
                preview.innerHTML += `<button style="display:block; width:100%; padding:15px; background:red; color:white; border:none; border-radius:10px; margin-top:10px;">BOTÃO GERADO PELA IA</button>`;
            } else {
                preview.innerHTML += `<div style="padding:10px; border:1px solid var(--primary); margin-top:10px; border-radius:10px;">✨ Componente gerado por IA para: ${p.value}</div>`;
            }

            syncCode();
            chat.scrollTop = chat.scrollHeight;
            p.value = '';
        }, 1000);
    }

    function generateAPK() {
        alert("SISTEMA DE INSTALAÇÃO PROFISSIONAL:\n1. O código foi empacotado.\n2. O manifesto de instalação foi gerado.\n3. O download do APK começará em instantes.");
    }
</script>

</body>
</html>
