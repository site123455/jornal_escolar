# jornal_escolar<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Jornal Escolar Afonso Pena</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      font-family: Arial, sans-serif;
      background-color: #f0f2f5;
      margin: 0;
      padding: 0;
      text-align: center;
    }

    header {
      background-color: #004080;
      color: white;
      padding: 20px;
    }

    header button {
      margin-top: 10px;
    }

    .site-content, .login-box, .admin-panel {
      display: none;
      margin: 30px auto;
      width: 90%;
      max-width: 700px;
    }

    .active {
      display: block;
    }

    .noticia {
      background: white;
      margin: 20px auto;
      padding: 20px;
      border-radius: 10px;
      width: 100%;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      text-align: left;
      position: relative;
    }

    .noticia h2 {
      cursor: pointer;
      color: #004080;
      margin: 0;
      font-size: 1.2em;
    }

    .conteudo {
      display: none;
      margin-top: 10px;
    }

    input, textarea {
      padding: 10px;
      margin: 10px auto;
      display: block;
      width: 100%;
      font-size: 16px;
    }

    button {
      padding: 10px 20px;
      margin: 10px 5px;
      border: none;
      background-color: #0077cc;
      color: white;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
    }

    textarea {
      resize: vertical;
    }

    #formNoticia {
      margin-top: 20px;
      display: none;
    }

    .acoes {
      display: flex;
      justify-content: flex-end;
      gap: 10px;
      margin-top: 10px;
    }

    .btn-acao {
      background: transparent;
      border: none;
      font-size: 20px;
      cursor: pointer;
      color: #555;
      transition: color 0.3s;
    }

    .btn-acao:hover {
      color: #0077cc;
    }

    @media (max-width: 768px) {
      header h1 {
        font-size: 1.5em;
      }

      button {
        font-size: 14px;
      }

      .noticia {
        padding: 15px;
      }

      .btn-acao {
        font-size: 18px;
      }
    }

    @media (max-width: 480px) {
      .noticia h2 {
        font-size: 1em;
      }

      .btn-acao {
        font-size: 16px;
      }

      input, textarea {
        font-size: 14px;
      }

      button {
        width: 100%;
        margin: 8px 0;
      }

      .acoes {
        justify-content: center;
      }
    }
  </style>
</head>
<body>

  <header>
    <h1>Jornal Escolar Afonso Pena</h1>
    <button onclick="mostrarLogin()">Administração</button>
  </header>

  <div class="site-content active" id="siteContent"></div>

  <div class="login-box" id="loginBox">
    <h2>Login do Administrador</h2>
    <input type="password" id="senha" placeholder="Digite a senha" />
    <button onclick="verificarSenha()">Entrar</button>
    <p id="erro" style="color:red;"></p>
  </div>

  <div class="admin-panel" id="adminPanel">
    <h2>Painel do Administrador</h2>
    <button onclick="mostrarFormulario()">+ Nova Notícia</button>
    <button onclick="sair()">Sair</button>

    <form id="formNoticia" onsubmit="criarOuEditarNoticia(event)">
      <input type="text" id="titulo" placeholder="Título da notícia" required />
      <textarea id="conteudo" placeholder="Conteúdo da notícia" rows="5" required></textarea>
      <input type="hidden" id="indiceEditar" value="" />
      <button type="submit">Salvar</button>
      <button type="button" onclick="cancelarFormulario()">Cancelar</button>
    </form>
  </div>

  <script>
    const senhaCorreta = "escola afonso pena 123455";
    let modoAdmin = false;

    function mostrarLogin() {
      document.getElementById('loginBox').classList.add('active');
      document.getElementById('siteContent').classList.remove('active');
      document.getElementById('adminPanel').classList.remove('active');
    }

    function verificarSenha() {
      const senha = document.getElementById('senha').value;
      if (senha === senhaCorreta) {
        modoAdmin = true;
        document.getElementById('loginBox').classList.remove('active');
        document.getElementById('adminPanel').classList.add('active');
        document.getElementById('siteContent').classList.add('active');
        document.getElementById('erro').textContent = "";
        atualizarNoticiasNaTela();
      } else {
        document.getElementById('erro').textContent = "Senha incorreta!";
      }
    }

    function mostrarFormulario(indice = null) {
      const form = document.getElementById('formNoticia');
      if (indice !== null) {
        const noticias = JSON.parse(localStorage.getItem('noticias')) || [];
        document.getElementById('titulo').value = noticias[indice].titulo;
        document.getElementById('conteudo').value = noticias[indice].conteudo;
        document.getElementById('indiceEditar').value = indice;
      } else {
        document.getElementById('formNoticia').reset();
        document.getElementById('indiceEditar').value = "";
      }
      form.style.display = 'block';
    }

    function cancelarFormulario() {
      document.getElementById('formNoticia').reset();
      document.getElementById('formNoticia').style.display = 'none';
    }

    function criarOuEditarNoticia(event) {
      event.preventDefault();
      const titulo = document.getElementById('titulo').value;
      const conteudo = document.getElementById('conteudo').value;
      const indiceEditar = document.getElementById('indiceEditar').value;

      let noticias = JSON.parse(localStorage.getItem('noticias')) || [];

      if (indiceEditar === "") {
        noticias.push({ titulo, conteudo });
      } else {
        noticias[indiceEditar] = { titulo, conteudo };
      }

      localStorage.setItem('noticias', JSON.stringify(noticias));
      document.getElementById('formNoticia').reset();
      document.getElementById('formNoticia').style.display = 'none';
      atualizarNoticiasNaTela();
    }

    function alternarConteudo(elemento) {
      const conteudo = elemento.nextElementSibling;
      conteudo.style.display = conteudo.style.display === 'block' ? 'none' : 'block';
    }

    function atualizarNoticiasNaTela() {
      const noticias = JSON.parse(localStorage.getItem('noticias')) || [];
      const container = document.getElementById('siteContent');
      container.innerHTML = "";

      noticias.forEach((noticia, index) => {
        const div = document.createElement('div');
        div.className = 'noticia';
        div.innerHTML = `
          <h2 onclick="alternarConteudo(this)">${noticia.titulo}</h2>
          <div class="conteudo"><p>${noticia.conteudo}</p></div>
        `;

        if (modoAdmin) {
          const acoesDiv = document.createElement('div');
          acoesDiv.className = 'acoes';

          const btnEditar = document.createElement('button');
          btnEditar.innerHTML = "✏️";
          btnEditar.className = "btn-acao";
          btnEditar.title = "Editar notícia";
          btnEditar.addEventListener('click', () => mostrarFormulario(index));

          const btnExcluir = document.createElement('button');
          btnExcluir.innerHTML = "🗑️";
          btnExcluir.className = "btn-acao";
          btnExcluir.title = "Excluir notícia";
          btnExcluir.addEventListener('click', () => apagarNoticia(index));

          acoesDiv.appendChild(btnEditar);
          acoesDiv.appendChild(btnExcluir);
          div.appendChild(acoesDiv);
        }

        container.appendChild(div);
      });
    }

    function apagarNoticia(indice) {
      if (confirm("Tem certeza que deseja apagar esta notícia?")) {
        let noticias = JSON.parse(localStorage.getItem('noticias')) || [];
        noticias.splice(indice, 1);
        localStorage.setItem('noticias', JSON.stringify(noticias));
        atualizarNoticiasNaTela();
      }
    }

    function sair() {
      modoAdmin = false;
      document.getElementById('adminPanel').classList.remove('active');
      document.getElementById('siteContent').classList.add('active');
      atualizarNoticiasNaTela();
    }

    // Carrega as notícias ao abrir
    atualizarNoticiasNaTela();
  </script>

</body>
</html>
