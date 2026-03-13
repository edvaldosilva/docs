# 🚀 [Nome do Seu Projeto]

### O que é isso?
[Escreva 1 ou 2 linhas resumindo o que o projeto faz e qual problema ele resolve. Ex: "Um script em Python para automatizar o download de relatórios do banco e salvar no Google Drive."]

---

## 🛑 ONDE EU PAREI? (Leia isso primeiro!)
> **Atenção:** Se você abriu este projeto depois de muito tempo, comece por aqui para situar o seu "eu do futuro".

* **Data da última alteração:** DD/MM/AAAA
* **Última coisa feita:** [Ex: Criei a tela de login, mas a autenticação com o Firebase ainda está falhando com erro 403.]
* **Próxima coisa a fazer (Ação Imediata):** [Ex: Investigar as regras de segurança do Firestore. **NÃO** comece a fazer a tela de perfil antes de arrumar o login!]
* **Ideias soltas que tive antes de parar:** [Ex: Talvez seja melhor usar Zustand em vez de Redux para o estado global, parece mais simples.]

---

## ⚙️ COMO RODAR ESTA MÁQUINA (Passo a passo)
> Não confie na sua memória. Abaixo estão os comandos exatos para compilar ou rodar o projeto hoje.

### 1. Pré-requisitos
* [Ex: Node.js v18+, Docker, Python 3.10]

### 2. Instalação
```bash
# Cole os comandos exatos aqui. Ex:
git clone [url_do_seu_repo]
cd [nome_da_pasta]
npm install # ou pip install -r requirements.txt
```

### 3. Variáveis de Ambiente (.env):
Quais chaves e segredos este projeto precisa para rodar? (NUNCA comite senhas aqui, apenas liste as variáveis necessárias).
Crie um arquivo ```.env``` na raiz baseado no ```.env.example``` (se houver) e preencha:
* ```DATABASE_URL=``` (URL de conexão local do Postgres)
* ```API_KEY_GOOGLE=``` (Pegar no console do GCP, projeto X)
* ```SECRET_JWT=``` (Pode ser qualquer string localmente)


### 4. Execução:
```bash
# Comando exato para subir a aplicação localmente. Ex:
# npm run dev 
# python app.py
# docker-compose up
```

---

### 🗺️ O MAPA (Visão Geral e Tarefas)

Qual era a visão original? O que já está pronto e o que falta?

* [x] Setup inicial do repositório e dependências

* [x] Conectar com o banco de dados

* [ ] Criar a API de cadastro de usuários (🚧 Em andamento)

* [ ] Integrar com meio de pagamento (Stripe)

* [ ] Fazer o deploy na Vercel/Render

---

### 🧠 DIÁRIO DE BORDO (Decisões e Gambiarras)

Por que você fez do jeito que fez? Anote aqui para não se questionar no futuro.

* **[DD/MM/AAAA] - Mudança de Banco:** Comecei com MongoDB, mas percebi que os dados eram muito relacionais. Mudei para PostgreSQL. Deu trabalho, mas foi melhor.

* **[DD/MM/AAAA] - Gambiarra temporária:** O upload de imagens está falhando se o arquivo for maior que 2MB. Coloquei um aviso no frontend provisoriamente. Preciso configurar o limite no backend (ver arquivo ```upload.js```).

* **[DD/MM/AAAA] - Lib abandonada:** Tentei usar a biblioteca ```xyz-graficos```, mas ela não recebe updates há 3 anos. Troquei pela ```chart.js```.
