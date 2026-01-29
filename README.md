<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Feedback's Fusion</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-zinc-950 text-white min-h-screen flex flex-col items-center">  <div class="w-full max-w-3xl p-4">
    <h1 class="text-3xl font-bold text-center mb-1">Feedback's Fusion</h1>
    <p class="text-center text-zinc-400 mb-4">Deixe sua avaliacão aqui</p><!-- Formulário -->
<div class="bg-zinc-900 p-4 rounded-xl shadow mb-6">
  <input id="nome" type="text" placeholder="Seu nome (opcional)" class="w-full mb-2 p-2 rounded bg-zinc-800" />

  <div class="flex items-center gap-2 mb-2">
    <span class="text-sm">Avaliação:</span>
    <div id="stars" class="flex gap-1">
      <span onclick="setStar(1)" class="cursor-pointer text-xl">⭐</span>
      <span onclick="setStar(2)" class="cursor-pointer text-xl">⭐</span>
      <span onclick="setStar(3)" class="cursor-pointer text-xl">⭐</span>
      <span onclick="setStar(4)" class="cursor-pointer text-xl">⭐</span>
      <span onclick="setStar(5)" class="cursor-pointer text-xl">⭐</span>
    </div>
  </div>

  <textarea id="mensagem" placeholder="Digite seu feedback" class="w-full p-2 rounded bg-zinc-800"></textarea>
  <button onclick="enviarFeedback()" class="mt-3 w-full bg-emerald-600 hover:bg-emerald-700 p-2 rounded font-semibold">Enviar Feedback</button>
</div>

<!-- Estatísticas -->
<div id="stats" class="grid grid-cols-2 gap-2 mb-4"></div>

<!-- Lista -->
<div id="lista" class="space-y-3"></div>

<!-- Área admin escondida -->
<div id="adminArea" class="hidden mt-8 bg-zinc-900 p-4 rounded-xl">
  <h2 class="font-semibold mb-2">Área do Administrador</h2>
  <input id="senha" type="password" placeholder="Senha de administrador" class="w-full mb-2 p-2 rounded bg-zinc-800" />
  <button onclick="ativarAdmin()" class="w-full bg-blue-600 hover:bg-blue-700 p-2 rounded">Entrar como Admin</button>
</div>

<!-- Botão invisível -->
<button onclick="mostrarAdmin()" class="fixed bottom-2 right-2 opacity-0">admin</button>

  </div><script>
  let admin = false;
  let stars = 5;
  const SENHA_ADMIN = "534726fusion";

  function setStar(n) {
    stars = n;
  }

  function enviarFeedback() {
    const nome = document.getElementById('nome').value || 'Anônimo';
    const mensagem = document.getElementById('mensagem').value;

    if (!mensagem.trim()) return alert('Digite um feedback.');

    const feedbacks = JSON.parse(localStorage.getItem('feedbacks') || '[]');
    feedbacks.unshift({ nome, mensagem, stars, data: new Date().toLocaleString() });
    localStorage.setItem('feedbacks', JSON.stringify(feedbacks));

    document.getElementById('mensagem').value = '';
    carregarFeedbacks();
  }

  function carregarFeedbacks() {
    const lista = document.getElementById('lista');
    lista.innerHTML = '';
    const feedbacks = JSON.parse(localStorage.getItem('feedbacks') || '[]');

    feedbacks.forEach((f, i) => {
      const div = document.createElement('div');
      div.className = 'bg-zinc-900 p-3 rounded-xl';
      div.innerHTML = `
        <div class="flex justify-between">
          <strong>${f.nome}</strong>
          <span class="text-xs text-zinc-400">${f.data}</span>
        </div>
        <div class="text-yellow-400">${'⭐'.repeat(f.stars)}</div>
        <p class="mt-2">${f.mensagem}</p>
        ${admin ? `<button onclick="deletar(${i})" class="mt-2 text-red-400 text-sm">Excluir</button>` : ''}
      `;
      lista.appendChild(div);
    });

    gerarStats(feedbacks);
  }

  function gerarStats(feedbacks) {
    const total = feedbacks.length;
    const media = total ? (feedbacks.reduce((a,b)=>a+b.stars,0)/total).toFixed(1) : 0;

    document.getElementById('stats').innerHTML = `
      <div class="bg-zinc-900 p-3 rounded-xl text-center">Total de feedbacks<br><strong class="text-xl">${total}</strong></div>
      <div class="bg-zinc-900 p-3 rounded-xl text-center">Média de avaliação<br><strong class="text-xl">${media} ⭐</strong></div>
    `;
  }

  function ativarAdmin() {
    const senha = document.getElementById('senha').value;
    if (senha === SENHA_ADMIN) {
      admin = true;
      sessionStorage.setItem('admin','true');
      alert('Modo administrador ativado!');
      carregarFeedbacks();
    } else alert('Senha incorreta');
  }

  function mostrarAdmin() {
    document.getElementById('adminArea').classList.toggle('hidden');
  }

  function deletar(index) {
    const feedbacks = JSON.parse(localStorage.getItem('feedbacks') || '[]');
    feedbacks.splice(index, 1);
    localStorage.setItem('feedbacks', JSON.stringify(feedbacks));
    carregarFeedbacks();
  }

  if (sessionStorage.getItem('admin') === 'true') admin = true;

  carregarFeedbacks();
</script></body>
