# Piña Club Barmans

Site profissional para serviços de bartending em Andradina-SP.

## 🚀 Configuração

### 1. Configurar Supabase

1. Crie uma conta em [supabase.com](https://supabase.com)
2. Crie um novo projeto
3. Vá em Settings → API e copie:
   - Project URL
   - anon public key

### 2. Configurar Variáveis de Ambiente

No Netlify, vá em **Site Settings → Environment Variables** e adicione:

```
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

### 3. Configurar Banco de Dados

Execute este SQL no Supabase SQL Editor:

```sql
-- Criar tabelas
CREATE TABLE IF NOT EXISTS gallery_items (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  url text NOT NULL,
  caption text NOT NULL,
  type text DEFAULT 'image' CHECK (type IN ('image', 'video')),
  created_at timestamptz DEFAULT now()
);

CREATE TABLE IF NOT EXISTS testimonials (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  client_name text NOT NULL,
  client_photo text NOT NULL,
  testimonial_text text DEFAULT '',
  rating integer DEFAULT 5 CHECK (rating >= 1 AND rating <= 5),
  event_type text NOT NULL,
  created_at timestamptz DEFAULT now()
);

CREATE TABLE IF NOT EXISTS site_settings (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  setting_key text UNIQUE NOT NULL,
  setting_value text NOT NULL,
  setting_type text DEFAULT 'text',
  created_at timestamptz DEFAULT now(),
  updated_at timestamptz DEFAULT now()
);

-- Habilitar RLS
ALTER TABLE gallery_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE testimonials ENABLE ROW LEVEL SECURITY;
ALTER TABLE site_settings ENABLE ROW LEVEL SECURITY;

-- Políticas de acesso
CREATE POLICY "Public read access" ON gallery_items FOR SELECT USING (true);
CREATE POLICY "Authenticated write access" ON gallery_items FOR ALL TO authenticated USING (true);

CREATE POLICY "Public read access" ON testimonials FOR SELECT USING (true);
CREATE POLICY "Authenticated write access" ON testimonials FOR ALL TO authenticated USING (true);

CREATE POLICY "Public read access" ON site_settings FOR SELECT USING (true);
CREATE POLICY "Authenticated write access" ON site_settings FOR ALL TO authenticated USING (true);
```

## 📱 Uso

- **Site Principal:** `/`
- **Painel Admin:** `/admin`

## 🛠️ Desenvolvimento

```bash
npm install
npm run dev
```

## 🚀 Deploy

O site é automaticamente deployado no Netlify quando você faz push para o repositório.

## 📞 Suporte

Para dúvidas sobre configuração, consulte o guia completo em `/admin` → aba "Guia de Setup".