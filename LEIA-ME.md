# Hub da Marcenaria — Guia de Deploy

## O que é isso?
Um sistema web para gerenciar seus projetos de marcenaria com busca por IA.
Acesse pelo navegador, no celular ou computador, de qualquer lugar.

---

## Passo a passo para colocar no ar

### 1. Criar conta no GitHub (gratuito)
1. Acesse https://github.com e clique em "Sign up"
2. Crie uma conta com seu e-mail
3. Confirme o e-mail

### 2. Criar repositório no GitHub
1. Depois de logar, clique no "+" no canto superior direito → "New repository"
2. Nome: `marcenaria-hub`
3. Marque "Public"
4. Clique em "Create repository"

### 3. Subir os arquivos
1. Na página do repositório, clique em "uploading an existing file"
2. Arraste TODOS os arquivos desta pasta para a tela
   - Mantenha a estrutura de pastas (public/css, public/js, etc)
3. Clique em "Commit changes"

### 4. Criar conta no Vercel (gratuito)
1. Acesse https://vercel.com
2. Clique em "Sign up" → "Continue with GitHub"
3. Autorize o Vercel a acessar seu GitHub

### 5. Publicar o site
1. No painel do Vercel, clique em "Add New Project"
2. Selecione o repositório `marcenaria-hub`
3. Clique em "Deploy"
4. Em ~30 segundos o site estará no ar!
5. O Vercel vai gerar um link tipo: `marcenaria-hub.vercel.app`

---

## Configurar a IA

1. Acesse https://console.anthropic.com
2. Crie uma conta (ou faça login)
3. Vá em "API Keys" → "Create Key"
4. Copie a chave (começa com `sk-ant-...`)
5. No seu site, vá em **Integrações** → cole a chave → Salvar

Pronto! A IA vai ler seus PDFs e responder perguntas sobre os projetos.

---

## Próximas integrações (em desenvolvimento)
- **Monday.com** — criar tarefas automaticamente
- **WhatsApp** — agente que responde clientes
- **Dalmobile** — sincronizar orçamentos

---

## Dúvidas?
Abra uma conversa no Claude e pergunte!
