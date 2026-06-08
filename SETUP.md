# 🚀 Guia de Deploy — Calculadora de Salário

## Visão Geral

- **Frontend:** GitHub Pages (gratuito)
- **Banco de dados:** Firebase Firestore (gratuito no plano Spark)
- **Autenticação:** PIN local, gerenciado dentro do app

---

## PASSO 1 — Criar projeto no Firebase

1. Acesse **https://console.firebase.google.com**
2. Clique em **"Criar projeto"**
3. Dê um nome (ex: `calculadora-salario`) e siga os passos
4. Após criar, clique em **"Firestore Database"** no menu lateral
5. Clique em **"Criar banco de dados"**
6. Escolha **"Iniciar no modo de teste"** → escolha a região `us-east1` (ou a mais próxima)
7. Clique em **Concluir**

---

## PASSO 2 — Obter as credenciais do Firebase

1. No console do Firebase, clique na engrenagem ⚙️ → **Configurações do projeto**
2. Role até **"Seus aplicativos"** → clique em **"</> Web"**
3. Registre o app (pode deixar o nome qualquer)
4. Copie o objeto `firebaseConfig` que aparecer. Exemplo:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "calculadora-salario.firebaseapp.com",
  projectId: "calculadora-salario",
  storageBucket: "calculadora-salario.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## PASSO 3 — Configurar o arquivo index.html

1. Abra o arquivo `index.html`
2. Encontre o bloco (por volta da linha 395):
```js
const firebaseConfig = {
  apiKey: "COLE_SUA_API_KEY",
  ...
```
3. Substitua com os dados copiados no Passo 2

---

## PASSO 4 — Aplicar as Regras do Firestore

1. No console Firebase → **Firestore → Regras**
2. Substitua o conteúdo pelo arquivo `firestore.rules`
3. Clique em **Publicar**

---

## PASSO 5 — Publicar no GitHub Pages

### 5.1 — Criar repositório

1. Acesse **https://github.com** e faça login
2. Clique em **"New repository"**
3. Nome: `calculadora-salario` (ou qualquer nome)
4. Deixe **público**
5. Clique em **"Create repository"**

### 5.2 — Fazer upload dos arquivos

**Opção A — pelo site (mais fácil):**
1. No repositório criado, clique em **"uploading an existing file"**
2. Arraste o arquivo `index.html` para a área de upload
3. Clique em **"Commit changes"**

**Opção B — pelo terminal:**
```bash
git init
git add index.html
git commit -m "primeiro commit"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/calculadora-salario.git
git push -u origin main
```

### 5.3 — Ativar o GitHub Pages

1. No repositório, vá em **Settings → Pages**
2. Em **"Source"**, selecione `Deploy from a branch`
3. Branch: `main`, pasta: `/ (root)`
4. Clique em **Save**
5. Aguarde 1-2 minutos
6. Sua URL será: `https://SEU_USUARIO.github.io/calculadora-salario/`

---

## PASSO 6 — Primeiro acesso (usuário Master)

Na primeira vez que abrir o app:
- O sistema cria automaticamente o **usuário Master** com PIN `0000`
- **Troque o PIN** imediatamente pelo painel admin
- Use o painel admin para adicionar os usuários comuns

> Para trocar o PIN do Master: no Firebase Console → Firestore → coleção `users` → documento `master` → edite o campo `pin`

---

## Estrutura do Firestore

```
users/
  master              → { name, role: "master", pin: "0000" }
  user_1234567890     → { name, role: "common", pin: "1234" }

prefs/
  master              → { diasTrabalhados, salarioFixo, ... }
  user_1234567890     → { ... }

history/
  auto_id             → {
    userId, userName, timestamp,
    inputs: { valorProduzido, dias, descanso, fixo, comissao, ... },
    results: { bruto, ir, inss, liquido, total, ... }
  }
```

---

## Como funciona o sistema

| Recurso | Usuário Comum | Usuário Master |
|---|---|---|
| Calcular salário | ✅ | ✅ |
| Salvar cálculo | ✅ | ✅ |
| Ver histórico próprio | ❌ (não implementado) | — |
| Ver histórico de TODOS | ❌ | ✅ (ícone no header) |
| Adicionar usuários | ❌ | ✅ |
| Excluir usuários | ❌ | ✅ |
| Usuários sabem que são monitorados | ❌ | — |

---

## Dúvidas frequentes

**O usuário comum sabe que está sendo monitorado?**
Não. O botão do painel admin só aparece para o Master. O salvamento do histórico acontece silenciosamente quando o usuário clica em "Salvar Cálculo".

**Posso ter mais de um Master?**
Atualmente não — o design tem um único Master. Para adicionar outro, crie diretamente no Firestore com `role: "master"`.

**Como redefinir o PIN de um usuário?**
Acesse o Firestore Console → coleção `users` → documento do usuário → edite o campo `pin`.
