# Padrão de Versionamento: Commits e Branches

Neste projeto, a desorganização não é tolerada. Para mantermos a sanidade da base de código, rastrearmos alterações de forma rigorosa e permitirmos a automação de lançamentos e *changelogs*, adotamos regras estritas de nomenclatura para as branches e a especificação **Conventional Commits** para o histórico.

Qualquer código submetido que não cumpra estritamente estas diretrizes será rejeitado sumariamente nos *code reviews* ou bloqueado por automação (*Git Hooks*).

## 1. Nomenclatura de Branches

O histórico e a organização começam no momento em que crias a tua branch. O formato obrigatório é:

` <tipo>/<id-da-tarefa>-<descricao-curta> `

*(Nota: O `<id-da-tarefa>` é opcional, mas obrigatório se estiveres a trabalhar com um sistema de gestão de tarefas como Jira, Trello ou Linear).*

### Prefixos Permitidos
Os prefixos das branches devem espelhar a intenção do trabalho e alinhar-se com os tipos de commit.
* **`feat/`**: Desenvolvimento de uma nova funcionalidade.
* **`fix/`**: Correção de um bug (em ambiente de desenvolvimento ou homologação).
* **`hotfix/`**: Correção crítica de um bug que **já está em produção** e exige intervenção imediata.
* **`chore/`**: Tarefas de manutenção, atualização de dependências, ou reconfigurações do ambiente (ex: atualizar pacotes do Vue.js, ajustar o SDK do Flutter).
* **`docs/`**: Alterações exclusivas à documentação.
* **`refactor/`**: Reestruturação de código que não altera o comportamento final do sistema.

### Regras Inegociáveis para Branches
1. **Minúsculas (Lowercase):** Tudo tem de ser escrito em minúsculas (a única exceção é a sigla do ticket, ex: `PROJ-123`).
2. **Hifens como separador:** É expressamente proibido o uso de espaços, *camelCase* ou *snake_case* (`_`). Usa sempre o hífen (`-`).
3. **Sê descritivo, mas conciso:** O nome da branch tem de explicar claramente o propósito sem obrigar outro programador a ir ler o código.
4. **Sem nomes próprios:** Nunca utilizes o teu nome ou iniciais na branch. O Git já regista a autoria do trabalho.

**Exemplos Corretos:**
* `feat/autenticacao-jwt`
* `fix/JIRA-452-crash-ecra-pagamento`
* `chore/atualiza-dependencias-backend`

**Exemplos Inaceitáveis (Não usar):**
* `teste-api` *(Vago. Teste de quê?)*
* `FeatureLogin` *(Falta a barra do prefixo e usa maiúsculas).*
* `fix/corrigindo_bug_urgente` *(Usa underscores em vez de hifens).*

---

## 2. Mensagens de Commit (Conventional Commits)

O teu commit não é um diário pessoal; é documentação técnica. Cada mensagem tem de explicar o "o quê" e o "porquê" de forma cirúrgica. A estrutura obrigatória é:

` <tipo>[escopo opcional]: <descrição> `

` [corpo opcional] `

` [rodapé opcional] `

### Os Tipos de Commit

* **`feat`**: Adiciona uma nova funcionalidade ao código. (Gera um salto `MINOR` no versionamento semântico).
* **`fix`**: Corrige um bug. (Gera um salto `PATCH` no versionamento semântico).
* **`build`**: Alterações que afetam o sistema de compilação ou dependências externas (ex: npm, pip, docker).
* **`chore`**: Tarefas de manutenção rotineiras que não afetam o código de produção nem os testes.
* **`ci`**: Alterações nos ficheiros e scripts de configuração de Integração Contínua (ex: pipelines, GitHub Actions).
* **`docs`**: Alterações exclusivamente na documentação.
* **`style`**: Formatação de código que não altera a lógica estrutural (espaços em branco, falta de ponto e vírgula, etc.).
* **`refactor`**: Uma alteração de código que não corrige um bug nem adiciona uma funcionalidade, apenas melhora a estrutura interna.
* **`perf`**: Uma alteração de código focada especificamente em melhorar o desempenho da aplicação.
* **`test`**: Adição de testes em falta ou correção de testes existentes.

### Regras de Ouro para os Commits

1. **A Descrição:** Tem de ser um resumo curto do que foi feito. Usa o modo imperativo (ex: "adiciona", "corrige", "remove"), não comeces com letra maiúscula e não coloques ponto final.
2. **O Escopo:** É opcional, mas recomendado em arquiteturas complexas. Deve ser um substantivo a descrever a secção do código afetada, envolto em parênteses (ex: `feat(api-retalho): ...`).
3. **Breaking Changes (Alterações Quebradas):** Se o teu commit introduz uma alteração destrutiva que quebra a compatibilidade com a versão anterior do software, **tens de** deixar isso claro. Adiciona um `!` logo a seguir ao tipo/escopo (ex: `feat!: ...`) e inclui `BREAKING CHANGE:` no rodapé com a explicação.

### Exemplos Reais

**Um commit simples com escopo:**
` feat(autenticacao): adiciona suporte a tokens JWT `

**Um commit de manutenção (chore):**
` chore: atualiza pacotes de linting para a versão mais recente `

**Um commit de correção direto ao ponto:**
` fix: resolve o crash inesperado ao carregar o dashboard `

**Um commit com Breaking Change destrutivo:**
` feat(api)!: altera a estrutura do payload de resposta `

` BREAKING CHANGE: o endpoint /users agora retorna um array em vez de um objeto. As integrações atuais vão falhar redondamente se não forem atualizadas. `

---
*Para garantires que não há margem para erros, adivinhações ou interpretações dúbias, lê a especificação completa e oficial.*
**Fonte e Autoria:** [Conventional Commits v1.0.0 (pt-br)](https://www.conventionalcommits.org/pt-br/v1.0.0/)