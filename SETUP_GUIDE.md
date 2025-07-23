# 🚀 Guia Completo: Configurar Netlify e Supabase

## 📋 **Índice**
1. [Configurar Supabase](#1-configurar-supabase)
2. [Configurar Netlify](#2-configurar-netlify)
3. [Conectar os Dois](#3-conectar-os-dois)
4. [Testar Funcionamento](#4-testar-funcionamento)
5. [Solução de Problemas](#5-solução-de-problemas)

---

## 1. 🗄️ **Configurar Supabase**

### **Passo 1.1: Criar Conta**
1. Acesse: [supabase.com](https://supabase.com)
2. Clique em **"Start your project"**
3. Faça login com GitHub, Google ou email

### **Passo 1.2: Criar Projeto**
1. Clique em **"New Project"**
2. Escolha uma **Organization** (ou crie uma)
3. Preencha:
   - **Name:** `pina-club-barmans`
   - **Database Password:** (crie uma senha forte)
   - **Region:** `South America (São Paulo)`
4. Clique em **"Create new project"**
5. ⏳ **Aguarde 2-3 minutos** para o projeto ser criado

### **Passo 1.3: Obter Credenciais**
1. No painel do Supabase, vá em **Settings** → **API**
2. **Copie e salve** estas informações:
   ```
   Project URL: https://xxxxxxxxx.supabase.co
   anon public: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
   ```

### **Passo 1.4: Configurar Banco de Dados**
1. Vá em **SQL Editor**
2. Clique em **"New query"**
3. **Cole e execute** este código:

```sql
-- Criar tabela de itens da galeria
CREATE TABLE IF NOT EXISTS gallery_items (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  url text NOT NULL,
  caption text NOT NULL,
  type text DEFAULT 'image' CHECK (type IN ('image', 'video')),
  created_at timestamptz DEFAULT now()
);

-- Criar tabela de depoimentos
CREATE TABLE IF NOT EXISTS testimonials (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  client_name text NOT NULL,
  client_photo text NOT NULL,
  testimonial_text text DEFAULT '',
  rating integer DEFAULT 5 CHECK (rating >= 1 AND rating <= 5),
  event_type text NOT NULL,
  created_at timestamptz DEFAULT now()
);

-- Criar tabela de configurações do site
CREATE TABLE IF NOT EXISTS site_settings (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  setting_key text UNIQUE NOT NULL,
  setting_value text NOT NULL,
  setting_type text DEFAULT 'text',
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);

-- Habilitar RLS (Row Level Security)
ALTER TABLE gallery_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE testimonials ENABLE ROW LEVEL SECURITY;
ALTER TABLE site_settings ENABLE ROW LEVEL SECURITY;

-- Políticas para galeria (leitura pública, escrita autenticada)
CREATE POLICY "Permitir leitura pública da galeria" ON gallery_items
  FOR SELECT TO anon, authenticated USING (true);

CREATE POLICY "Permitir inserção para usuários autenticados" ON gallery_items
  FOR INSERT TO authenticated WITH CHECK (true);

CREATE POLICY "Permitir atualização para usuários autenticados" ON gallery_items
  FOR UPDATE TO authenticated USING (true);

CREATE POLICY "Permitir exclusão para usuários autenticados" ON gallery_items
  FOR DELETE TO authenticated USING (true);

-- Políticas para depoimentos
CREATE POLICY "Permitir leitura pública dos depoimentos" ON testimonials
  FOR SELECT TO anon, authenticated USING (true);

CREATE POLICY "Permitir inserção para usuários autenticados" ON testimonials
  FOR INSERT TO authenticated WITH CHECK (true);

CREATE POLICY "Permitir atualização para usuários autenticados" ON testimonials
  FOR UPDATE TO authenticated USING (true);

CREATE POLICY "Permitir exclusão para usuários autenticados" ON testimonials
  FOR DELETE TO authenticated USING (true);

-- Políticas para configurações do site
CREATE POLICY "Permitir leitura pública das configurações" ON site_settings
  FOR SELECT TO anon, authenticated USING (true);

CREATE POLICY "Permitir atualização para usuários autenticados" ON site_settings
  FOR ALL TO authenticated USING (true) WITH CHECK (true);
```

4. Clique em **"Run"** para executar
5. ✅ **Sucesso!** Tabelas criadas

---

## 2. 🌐 **Configurar Netlify**

### **Passo 2.1: Fazer Deploy**
1. Seu site já está no Netlify (foi feito deploy automaticamente)
2. Acesse o **dashboard do Netlify**: [app.netlify.com](https://app.netlify.com)
3. Encontre seu site na lista

### **Passo 2.2: Configurar Variáveis de Ambiente**
1. Clique no seu site
2. Vá em **Site settings**
3. No menu lateral, clique em **Environment variables**
4. Clique em **"Add a variable"**

### **Passo 2.3: Adicionar Variáveis**
Adicione estas **2 variáveis** (uma por vez):

**Variável 1:**
- **Key:** `VITE_SUPABASE_URL`
- **Value:** `https://xxxxxxxxx.supabase.co` (sua URL do Supabase)
- **Scopes:** Deixe marcado "All deploy contexts"

**Variável 2:**
- **Key:** `VITE_SUPABASE_ANON_KEY` (ou `VITE_SUPABASE_KEY`)
- **Value:** `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...` (sua chave anon)
- **Scopes:** Deixe marcado "All deploy contexts"

### **Passo 2.4: Fazer Novo Deploy**
1. Vá em **Deploys**
2. Clique em **"Trigger deploy"** → **"Deploy site"**
3. ⏳ **Aguarde** o deploy terminar (1-2 minutos)

---

## 3. 🔗 **Conectar os Dois**

### **Passo 3.1: Verificar Conexão**
1. Acesse seu site no Netlify
2. Vá para `/admin` (adicione /admin na URL)
3. **Verifique** se aparece:
   - ✅ **"Supabase Conectado"** (verde)
   - ❌ **"Supabase Não Configurado"** (vermelho)

### **Passo 3.2: Se Ainda Não Funcionar**
1. **Verifique** se as variáveis estão corretas
2. **Refaça** o deploy no Netlify
3. **Aguarde** alguns minutos para propagar

---

## 4. ✅ **Testar Funcionamento**

### **Teste 1: Upload de Imagem**
1. Vá para `/admin`
2. Na aba **"Galeria"**
3. Faça upload de uma imagem
4. Adicione uma legenda
5. Clique em **"Adicionar à Galeria"**
6. ✅ **Deve aparecer** "Upload realizado com sucesso!"

### **Teste 2: Verificar no Site**
1. Volte para a página inicial
2. Role até a seção **"Galeria de Eventos"**
3. ✅ **Deve aparecer** sua imagem

### **Teste 3: Configurar Imagem Principal**
1. Vá para `/admin`
2. Na aba **"Imagem Principal"**
3. Faça upload de uma nova imagem
4. Clique em **"Atualizar Imagem"**
5. ✅ **Deve aparecer** "Imagem atualizada!"

---

## 5. 🔧 **Solução de Problemas**

### **Problema: "Supabase Não Configurado"**
**Solução:**
1. Verifique se as variáveis estão corretas no Netlify
2. Certifique-se que não há espaços extras
3. Refaça o deploy

### **Problema: "Erro ao fazer upload"**
**Solução:**
1. Verifique se as tabelas foram criadas no Supabase
2. Execute novamente o SQL do Passo 1.4
3. Verifique as políticas RLS

### **Problema: Site não carrega**
**Solução:**
1. Verifique os logs no Netlify (Deploy log)
2. Verifique se o build foi bem-sucedido
3. Tente um novo deploy

### **Problema: Imagens não aparecem**
**Solução:**
1. Verifique se o upload foi bem-sucedido
2. Olhe a aba Network no navegador (F12)
3. Verifique se há erros de CORS

---

## 📞 **Suporte**

### **Links Úteis:**
- [Documentação Supabase](https://supabase.com/docs)
- [Documentação Netlify](https://docs.netlify.com)
- [Supabase Dashboard](https://app.supabase.com)
- [Netlify Dashboard](https://app.netlify.com)

### **Comandos Úteis:**
```bash
# Verificar variáveis localmente
echo $VITE_SUPABASE_URL
echo $VITE_SUPABASE_ANON_KEY

# Testar conexão Supabase
curl -H "apikey: SUA_CHAVE" "SUA_URL/rest/v1/"
```

---

## 🎉 **Parabéns!**

Se chegou até aqui, seu site está **100% funcional** com:
- ✅ **Banco de dados** configurado
- ✅ **Painel admin** funcionando
- ✅ **Upload de imagens** funcionando
- ✅ **Sincronização** em tempo real
- ✅ **Deploy automático** no Netlify

**Seu site profissional está no ar! 🚀**