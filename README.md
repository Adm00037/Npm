index.html<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Blacklist</title>
    <style>
        body { font-family: sans-serif; background: #121212; color: white; text-align: center; padding: 20px; }
        .container { max-width: 600px; margin: auto; background: #1e1e1e; padding: 20px; border-radius: 10px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }
        input, button { padding: 10px; margin: 5px; border-radius: 5px; border: none; }
        input { width: 70%; }
        button { cursor: pointer; background: #e74c3c; color: white; font-weight: bold; }
        button:hover { background: #c0392b; }
        .panel { display: none; margin-top: 20px; border-top: 1px solid #444; padding-top: 20px; }
        .log-entry { font-size: 0.8em; color: #bbb; border-bottom: 1px solid #333; padding: 5px; }
        #resultado { margin-top: 20px; font-weight: bold; color: #f1c40f; }
    </style>
</head>
<body>

<div class="container">
    <h2>🔍 Consulta Blacklist</h2>
    <input type="text" id="searchName" placeholder="Digite o Nick do jogador...">
    <button onclick="buscar()">Pesquisar</button>
    <div id="resultado"></div>

    <hr>

    <h3>Acesso Administrativo</h3>
    <input type="text" id="adminUser" placeholder="Login (Seu nome ou Arthur)">
    <input type="password" id="adminPass" placeholder="Senha">
    <button onclick="acessarPainel()">Entrar</button>

    <div id="painelEdit" class="panel">
        <h3>➕ Adicionar/Remover</h3>
        <input type="text" id="newName" placeholder="Nome do Jogador">
        <input type="text" id="newReason" placeholder="Motivo do Ban">
        <button onclick="adicionar()">Adicionar</button>
        <hr>
        <input type="text" id="removeName" placeholder="Nome para remover">
        <button onclick="remover()">Remover</button>
    </div>

    <div id="painelLog" class="panel">
        <h3>📜 Histórico de Atividades</h3>
        <div id="logContent"></div>
        <button onclick="limparLogs()" style="background: #555;">Limpar Histórico</button>
    </div>
</div>

<script>
    // Inicializar dados se não existirem
    let blacklist = JSON.parse(localStorage.getItem('blacklist')) || {};
    let logs = JSON.parse(localStorage.getItem('logs')) || [];

    function buscar() {
        const name = document.getElementById('searchName').value.trim();
        const res = document.getElementById('resultado');
        if (blacklist[name]) {
            res.innerHTML = `⚠️ JOGADOR NA BLACKLIST!<br>Motivo: ${blacklist[name]}`;
            res.style.color = "#e74c3c";
        } else {
            res.innerHTML = "✅ Jogador Limpo.";
            res.style.color = "#2ecc71";
        }
    }

    function acessarPainel() {
        const user = document.getElementById('adminUser').value;
        const pass = document.getElementById('adminPass').value;

        // Resetar painéis
        document.getElementById('painelEdit').style.display = 'none';
        document.getElementById('painelLog').style.display = 'none';

        if (pass === 'npm') {
            if (user.toLowerCase() === 'arthur') {
                document.getElementById('painelLog').style.display = 'block';
                renderLogs();
            } else {
                document.getElementById('painelEdit').style.display = 'block';
            }
            registrarLog(user, "Acessou o sistema");
        } else {
            alert("Senha incorreta!");
        }
    }

    function adicionar() {
        const user = document.getElementById('adminUser').value;
        const name = document.getElementById('newName').value.trim();
        const reason = document.getElementById('newReason').value.trim();

        if (name && reason) {
            blacklist[name] = reason;
            save();
            registrarLog(user, `Adicionou: ${name}`);
            alert("Adicionado com sucesso!");
        }
    }

    function remover() {
        const user = document.getElementById('adminUser').value;
        const name = document.getElementById('removeName').value.trim();

        if (blacklist[name]) {
            delete blacklist[name];
            save();
            registrarLog(user, `Removeu: ${name}`);
            alert("Removido!");
        }
    }

    function registrarLog(usuario, acao) {
        const data = new Date().toLocaleString();
        logs.push({ usuario, acao, data });
        localStorage.setItem('logs', JSON.stringify(logs));
    }

    function renderLogs() {
        const container = document.getElementById('logContent');
        container.innerHTML = logs.map(l => `
            <div class="log-entry"><b>[${l.data}]</b> ${l.usuario}: ${l.acao}</div>
        `).reverse().join('');
    }

    function limparLogs() {
        logs = [];
        localStorage.setItem('logs', JSON.stringify(logs));
        renderLogs();
    }

    function save() {
        localStorage.setItem('blacklist', JSON.stringify(blacklist));
    }
</script>

</body>
</html>
