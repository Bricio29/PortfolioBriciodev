# Portfólio Briciodev

Olá a todos! 👋

Permitam-me apresentar-me. Sou **Fabrício Weslley**. Nesta ocasião, gostaria de compartilhar o projeto de site de portfólio que desenvolvi.

## 🚀 Demonstração ao vivo

**Link do site:** [https://portfolio-briciodev-txw2.vercel.app/](https://portfolio-briciodev-txw2.vercel.app/)

## 🛠️ Pilha de tecnologia

Este projeto foi desenvolvido utilizando tecnologias web modernas:

- **ReactJS** - Framework front-end;
- **Tailwind CSS** - Framework CSS que prioriza utilitários;
- **AOS** - Biblioteca Animate On Scroll;
- **Firebase** - Serviços de back-end para dados de portfólio;
- **Supabase** - Back-end para sistema de comentários;
- **Framer Motion** - Biblioteca de animação;
- **Lucide** - Biblioteca de ícones;
- **Material UI** - Biblioteca de componentes React;
- **SweetAlert2** - Caixas de diálogo de alerta atraentes;

## 📋 Pré-requisitos

Antes de executar este projeto, certifique-se de ter o seguinte instalado:

- **Node.js** (versão 14.x ou superior)
- Gerenciador de pacotes **npm** ou **yarn**

## 🏃‍♂️ Primeiros passos

Siga estes passos para executar o projeto localmente:

### 1. Clonar o Repositório

```bash
git clone https://github.com/Bricio29/PortfolioBriciodev.git
cd PortfolioBriciodev
```

### 2. Instale as Dependências

```bash
npm install
```

Se você encontrar problemas de dependência de pares, use:

```bash
npm install --legacy-peer-deps
```

### 3. Execute o servidor de desenvolvimento

```bash
npm run dev
```

### 4. Abra no navegador

Acesse o aplicativo através do link exibido no seu terminal (geralmente `http://localhost:5173`).

## 🏗️ Construindo para Produção

Para criar uma compilação pronta para produção:

1. Execute o comando build:

   ```bash
   npm run build
   ```

2. Os arquivos de compilação serão salvos na pasta `dist`. Envie esta pasta para o seu servidor de hospedagem.

## ⚙️ Configuração

### Configuração do Firebase (Dados do Portfólio)

Os dados do portfólio são armazenados no Firebase Firestore. Para configurar o Firebase:

1. **Criar Projeto do Firebase:**

- Acesse o [Console do Firebase](https://console.firebase.google.com/)
- Crie um novo projeto ou use um existente

2. **Habilitar o Banco de Dados do Firestore:**

- Navegue até o Banco de Dados do Firestore
- Crie um banco de dados em modo de produção

3. **Obter Configuração:**

- Acesse as Configurações do Projeto (ícone de engrenagem)
- Role para baixo até a seção "Seus aplicativos"
- Copie o objeto de configuração do Firebase

4. **Definir Regras do Firestore:**

```javascript
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
        allow read: if true;
        allow write: if false;
       }
     }
   }
```

5. **Estrutura da Coleção:**
   Configure suas coleções do Firestore conforme mostrado abaixo:

![Exemplo de Estrutura de Coleção 1](https://github.com/user-attachments/assets/8d7cec06-88ee-425e-b693-6384c908062e)

![Exemplo de Estrutura de Coleção 2](https://github.com/user-attachments/assets/7da52ebf-6967-4fb4-b3c3-a329affe878a)

6. **Configuração das Variáveis ​​de Ambiente:**
   Adicione sua configuração do Firebase ao arquivo `.env`:

```env
# Configuração do Firebase
VITE_FIREBASE_API_KEY=your-firebase-api-key
VITE_FIREBASE_AUTH_DOMAIN=seu-projeto.firebaseapp.com
VITE_FIREBASE_DATABASE_URL=https://seu-projeto-default-rtdb.firebaseio.com
VITE_FIREBASE_PROJECT_ID=seu-projeto-id
VITE_FIREBASE_STORAGE_BUCKET=seu-projeto.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=seu-remetente-de-mensagens-id
VITE_FIREBASE_APP_ID=seu-aplicativo-id
```

7. **Atualizar arquivo de configuração:**
   O arquivo `firebase.js` deve usar variáveis ​​de ambiente:

```javascript
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  databaseURL: import.meta.env.VITE_FIREBASE_DATABASE_URL,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messageSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
```

### Configuração do Supabase (Sistema de Comentários)

O sistema de comentários é alimentado pelo Supabase. Para configurar o Supabase:

1. **Criar Projeto Supabase:**

- Acesse [Supabase](https://supabase.com/)
- Crie um novo projeto

2. **Criar Tabela de Comentários e Configuração:**
   Execute o seguinte SQL no seu Editor SQL do Supabase:

```sql
-- Crie a tabela portfolio_comments
CREATE TABLE portfolio_comments (
   id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
   content TEXT NOT NULL,
   user_name VARCHAR(255) NOT NULL,
   profile_image TEXT,
   is_pinned BOOLEAN DEFAULT FALSE,
   created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Cria um índice para melhor desempenho
CREATE INDEX idx_portfolio_comments_created_at ON portfolio_comments(created_at DESC);
CREATE INDEX idx_portfolio_comments_pinned ON portfolio_comments(is_pinned);

-- Habilitar Segurança em Nível de Linha (RLS)
ALTER TABLE portfolio_comments ENABLE ROW LEVEL SECURITY;

-- Criar políticas para acesso público
-- Permitir que o público leia todos os comentários
CREATE POLICY "Allow public read access on portfolio_comments"
ON portfolio_comments FOR SELECT
TO public
USING (true);

-- Permitir que o público insira comentários (mas não os fixados)
CREATE POLICY "Allow public insert on portfolio_comments"
ON portfolio_comments FOR INSERT
TO public
WITH CHECK (is_pinned = false);

-- Criar bucket de armazenamento para imagens de perfil
INSERT INTO storage.buckets (id, name, public)
VALUES ('profile-images', 'profile-images', true);

-- Criar política de armazenamento para imagens de perfil
CREATE POLICY "Allow public to upload profile images"
ON storage.objects FOR INSERT
TO public
WITH CHECK (bucket_id = 'profile-images');

CREATE POLICY "Allow public to read profile images"
ON storage.objects FOR SELECT
TO public
USING (bucket_id = 'profile-images');

```

3. **Habilitar Tempo Real:**

- Acesse o Editor de Tabelas
- Clique na sua tabela `portfolio_comments`
- Clique em "Editar Tabela"
- Habilite "Tempo Real" na configuração da tabela

4. **Obter Chaves de API:**

- Acesse Configurações > API
- Copie a URL do seu projeto e a chave pública anônima

## 🔧 Configuração de Variáveis ​​de Ambiente

Crie um arquivo `.env` na raiz do seu projeto com as configurações do Firebase e do Supabase:

```env
# Configuração do Firebase
VITE_FIREBASE_API_KEY=your-firebase-api-key
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_DATABASE_URL=https://your-project-default-rtdb.firebaseio.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=your-messaging-sender-id
VITE_FIREBASE_APP_ID=your-app-id

# Configuração do Supabase
VITE_SUPABASE_URL=seu-supabase-url
VITE_SUPABASE_ANON_KEY=sua-supabase-anon-key
```

**Importante:**

- Todas as variáveis ​​de ambiente devem ter o prefixo `VITE_` para que o Vite as acesse.
- Reinicie o servidor de desenvolvimento após criar/modificar o arquivo `.env`.
- Nunca envie seu arquivo `.env` para o controle de versão (adicione-o a `.gitignore`).

5. **Configuração das Variáveis ​​de Ambiente (Já abordado acima)**

6. **Arquivo de Configuração:**
O projeto usa `supabase-config-comment.js` com variáveis ​​de ambiente:

```javascript
import { createClient } from "@supabase/supabase-js";

// Acesse variáveis ​​de ambiente usando import.meta.env para Vite
const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
const supabaseKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

   if (!supabaseUrl || !supabaseKey) {
     console.error("Supabase URL:", supabaseUrl);
     console.error("Supabase Anon Key:", supabaseKey);
     throw new Error(
       "Supabase URL and Anon Key are required. Check your .env file and ensure they are prefixed with VITE_ and the dev server was restarted."
     );
   }

   export const supabase = createClient(supabaseUrl, supabaseKey);
   ```

**Importante:** certifique-se de reiniciar seu servidor de desenvolvimento após criar/modificar o arquivo `.env`.

## 🚨 Solução de problemas

Se você encontrar problemas ao executar o projeto:

- Certifique-se de que o Node.js esteja instalado corretamente
- Verifique se você está no diretório correto do projeto
- Verifique se todas as dependências estão instaladas sem erros
- Certifique-se de que suas configurações do Firebase e do Supabase estejam corretas
- Limpe o cache do seu navegador e tente novamente

## 📞 Contato

Se tiver alguma dúvida ou precisar de ajuda com a configuração, sinta-se à vontade para entrar em contato!

**Fabrício Weslley Cassimiro**

- Website: [https://portfolio-briciodev-txw2.vercel.app/](https://portfolio-briciodev-txw2.vercel.app/)
- GitHub: [Bricio29](https://github.com/Bricio29)

---

⭐ Se voce curtiu este projeto, por favor, considere dar uma estrela no GitHub!
