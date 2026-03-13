# Arquitetura de Acesso Remoto: SSH over AWS SSM em Windows Server

Este guia detalha a configuração de uma infraestrutura de acesso remoto seguro e sem senhas para instâncias EC2 Windows na AWS. 

O objetivo principal desta arquitetura é fornecer um **Pseudo-Terminal (PTY) nativo e perfeito** no Windows, permitindo a execução de aplicações TUI (Text User Interface) complexas (como as construídas em Golang com Bubble Tea ou Rust com Ratatui) sem as falhas de renderização comuns do AWS Session Manager nativo.

## 🏗️ O Problema Arquitetural

O uso nativo do AWS Systems Manager (SSM) Session Manager para acessar instâncias Windows não aloca um PTY real (ConPTY). Ele cria um processo oculto do PowerShell e trafega apenas *stdout/stderr*. Isso quebra aplicações TUI, causando problemas de renderização (flickering), falha na captura de eventos de teclado (setas, tab) e não reconhecimento de dimensões da janela.

## 💡 A Solução: SSH over SSM

Encapsulamos o protocolo SSH dentro do túnel seguro (websocket) do AWS SSM.
* **Segurança máxima:** A porta 22 (SSH) permanece **fechada** no Security Group para a internet.
* **Terminal Perfeito:** O OpenSSH Server roda internamente no Windows, fornecendo o PTY necessário para interfaces baseadas em texto.
* **Automação:** Autenticação via chaves assimétricas (Ed25519) elimina a necessidade de senhas.

---

## 🛠️ Pré-requisitos (AWS)

Antes de iniciar, garanta que a instância EC2 Windows possua:
1. Status de rede comunicando com a internet (NAT Gateway ou IGW).
2. Uma **IAM Role (Instance Profile)** anexada contendo a política gerenciada `AmazonSSMManagedInstanceCore`.

---

## Fase 1: Configuração do Cliente (Máquina Local)

### 1. Autenticação na AWS
Configure as credenciais do seu usuário IAM (que deve ter permissão para `ssm:StartSession`) usando um *profile* nomeado. No seu terminal local:

```bash
aws configure --profile meu-projeto
```
*Insira sua Access Key, Secret Key e defina a região padrão (ex: `us-east-1`).*

### 2. Instalação do AWS Session Manager Plugin
O túnel SSM exige um binário específico. Para instalar silenciosamente no Windows, execute no PowerShell local (como Administrador):

```powershell
Invoke-WebRequest -Uri "[https://s3.amazonaws.com/session-manager-downloads/plugin/latest/windows/SessionManagerPluginSetup.exe](https://s3.amazonaws.com/session-manager-downloads/plugin/latest/windows/SessionManagerPluginSetup.exe)" -OutFile "$env:USERPROFILE\Downloads\SessionManagerPluginSetup.exe"
Start-Process -FilePath "$env:USERPROFILE\Downloads\SessionManagerPluginSetup.exe" -ArgumentList "/quiet" -Wait
Write-Host "Instalação concluída. Reinicie o terminal para carregar o PATH." -ForegroundColor Green
```

### 3. Configuração do ProxyCommand (O Túnel)
Crie a regra que intercepta conexões SSH para instâncias AWS e as roteia pelo SSM.

Crie ou edite o arquivo `~/.ssh/config` (`C:\Users\SeuUsuario\.ssh\config` no Windows). **Atenção: O arquivo não pode ter extensão `.txt`.**

```text
# Roteia conexões SSH de instâncias AWS (i-*) para o túnel do SSM
host i-* mi-*
    ProxyCommand aws ssm start-session --target %h --document-name AWS-StartSSHSession --parameters "portNumber=%p" --profile meu-projeto
```

### 4. Geração da Chave Criptográfica
Gere o par de chaves Ed25519 (o padrão mais moderno e seguro). No terminal local:

```bash
ssh-keygen -t ed25519 -C "admin@meu-projeto"
```
Copie o conteúdo da chave pública recém-gerada (`cat ~/.ssh/id_ed25519.pub`). Você precisará dela na próxima fase.

---

## Fase 2: Configuração do Servidor (Instância EC2 Windows)

Conecte-se temporariamente à instância via AWS Console (Session Manager web) ou RDP para executar o bootstrap do servidor.

### 1. Instalar e Iniciar o OpenSSH Server
Execute em um prompt do PowerShell como Administrador:

```powershell
# Instala o serviço OpenSSH nativo do Windows
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

# Inicia o serviço e o configura para boot automático
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
```

### 2. Configurar o Acesso sem Senha (Autorização da Chave Pública)
No Windows Server, chaves de usuários pertencentes ao grupo de Administradores não ficam na pasta do usuário. Elas obrigatoriamente vão para `C:\ProgramData` e exigem permissões estritas de ACL (Access Control List).

Execute o script abaixo no PowerShell do servidor, substituindo o valor de `$PubKey` pela chave gerada no Passo 4 da Fase 1:

```powershell
# 1. Defina a sua chave pública
$PubKey = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... admin@meu-projeto"

# 2. Caminho obrigatório para administradores
$KeyPath = "$env:ProgramData\ssh\administrators_authorized_keys"

# 3. Cria a pasta/arquivo e injeta a chave
if (!(Test-Path "$env:ProgramData\ssh")) { New-Item -ItemType Directory -Path "$env:ProgramData\ssh" }
Add-Content -Force -Path $KeyPath -Value $PubKey

# 4. Ajuste estrito de permissões (Fundamental)
# Remove herança e garante acesso exclusivo ao SYSTEM e Administrators
icacls.exe $KeyPath /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"
```

---

## Fase 3: Conexão e Validação

De volta à sua máquina local, a infraestrutura está pronta. Execute o comando SSH usando diretamente o ID da instância:

```bash
ssh Administrator@i-0abcd1234efgh5678
```

**Comportamento Esperado:**
1. Na primeira conexão, o SSH perguntará se você confia no host (digite `yes`).
2. A AWS interceptará o comando via ProxyCommand, autenticará com seu profile e abrirá o websocket.
3. O OpenSSH do Windows validará sua chave Ed25519.
4. Você será logado instantaneamente no PowerShell interativo (`PS C:\Users\Administrator>`) sem solicitar senha.

Você agora possui um terminal robusto (ConPTY ativado) isolado da internet pública, pronto para compilar, transferir (via `scp`) e executar aplicações TUI.