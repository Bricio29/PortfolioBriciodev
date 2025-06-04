# Portfólio V5

Olá a todos! 👋

Permitam-me apresentar-me. Sou **Eki Zulfar Rachman**. Nesta ocasião, gostaria de compartilhar o projeto de site de portfólio que desenvolvi.

## 🚀 Demonstração ao vivo

**Link do site:** [https://www.eki.my.id/](https://www.eki.my.id/)

## 🛠️ Pilha de tecnologia

Este projeto foi desenvolvido utilizando tecnologias web modernas:

- **ReactJS** - Framework front-end
- **Tailwind CSS** - Framework CSS que prioriza utilitários
- **AOS** - Biblioteca Animate On Scroll
- **Firebase** - Serviços de back-end para dados de portfólio
- **Supabase** - Back-end para sistema de comentários
- **Framer Motion** - Biblioteca de animação
- **Lucide** - Biblioteca de ícones
- **Material UI** - Biblioteca de componentes React
- **SweetAlert2** - Caixas de diálogo de alerta atraentes

## 📋 Prerequisites

Before running this project, ensure you have the following installed:

- **Node.js** (version 14.x or higher)
- **npm** or **yarn** package manager

## 🏃‍♂️ Getting Started

Follow these steps to run the project locally:

### 1. Clone the Repository

```bash
git clone https://github.com/EkiZR/Portofolio_V5.git
cd Portofolio_V5
```

### 2. Install Dependencies

```bash
npm install
```

If you encounter peer dependency issues, use:

```bash
npm install --legacy-peer-deps
```

### 3. Run the Development Server

```bash
npm run dev
```

### 4. Open in Browser

Access the application through the link displayed in your terminal (usually `http://localhost:5173`).

## 🏗️ Building for Production

To create a production-ready build:

1. Run the build command:
   ```bash
   npm run build
   ```

2. The build files will be saved in the `dist` folder. Upload this folder to your hosting server.

## ⚙️ Configuration

### Firebase Configuration (Portfolio Data)

The portfolio data is stored in Firebase Firestore. To configure Firebase:

1. **Create Firebase Project:**
   - Go to the [Firebase Console](https://console.firebase.google.com/)
   - Create a new project or use an existing one

2. **Enable Firestore Database:**
   - Navigate to Firestore Database
   - Create a database in production mode

3. **Get Configuration:**
   - Go to Project Settings (gear icon)
   - Scroll down to "Your apps" section
   - Copy the Firebase configuration object

4. **Set Firestore Rules:**
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

5. **Collection Structure:**
   Set up your Firestore collections as shown below:

   ![Collection Structure Example 1](https://github.com/user-attachments/assets/8d7cec06-88ee-425e-b693-6384c908062e)

   ![Collection Structure Example 2](https://github.com/user-attachments/assets/7da52ebf-6967-4fb4-b3c3-a329affe878a)

6. **Environment Variables Setup:**
   Add your Firebase configuration to the `.env` file:
   ```env
   # Firebase Configuration
   VITE_FIREBASE_API_KEY=your-firebase-api-key
   VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
   VITE_FIREBASE_DATABASE_URL=https://your-project-default-rtdb.firebaseio.com
   VITE_FIREBASE_PROJECT_ID=your-project-id
   VITE_FIREBASE_STORAGE_BUCKET=your-project.firebasestorage.app
   VITE_FIREBASE_MESSAGING_SENDER_ID=your-messaging-sender-id
   VITE_FIREBASE_APP_ID=your-app-id
   ```

7. **Update Configuration File:**
   The `firebase.js` file should use environment variables:
   ```javascript
   import { initializeApp } from 'firebase/app';
   import { getFirestore } from 'firebase/firestore';

   const firebaseConfig = {
     apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
     authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
     databaseURL: import.meta.env.VITE_FIREBASE_DATABASE_URL,
     projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
     storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
     messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
     appId: import.meta.env.VITE_FIREBASE_APP_ID
   };

   const app = initializeApp(firebaseConfig);
   export const db = getFirestore(app);
   ```

### Supabase Configuration (Comment System)

The comment system is powered by Supabase. To configure Supabase:

1. **Create Supabase Project:**
   - Go to [Supabase](https://supabase.com/)
   - Create a new project

2. **Create Comments Table and Setup:**
   Run the following SQL in your Supabase SQL Editor:
   
   ```sql
   -- Create the portfolio_comments table
   CREATE TABLE portfolio_comments (
     id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
     content TEXT NOT NULL,
     user_name VARCHAR(255) NOT NULL,
     profile_image TEXT,
     is_pinned BOOLEAN DEFAULT FALSE,
     created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
   );

   -- Create an index for better performance
   CREATE INDEX idx_portfolio_comments_created_at ON portfolio_comments(created_at DESC);
   CREATE INDEX idx_portfolio_comments_pinned ON portfolio_comments(is_pinned);

   -- Enable Row Level Security (RLS)
   ALTER TABLE portfolio_comments ENABLE ROW LEVEL SECURITY;

   -- Create policies for public access
   -- Allow public to read all comments
   CREATE POLICY "Allow public read access on portfolio_comments"
   ON portfolio_comments FOR SELECT
   TO public
   USING (true);

   -- Allow public to insert comments (but not pinned ones)
   CREATE POLICY "Allow public insert on portfolio_comments"
   ON portfolio_comments FOR INSERT
   TO public
   WITH CHECK (is_pinned = false);

   -- Create storage bucket for profile images
   INSERT INTO storage.buckets (id, name, public)
   VALUES ('profile-images', 'profile-images', true);

   -- Create storage policy for profile images
   CREATE POLICY "Allow public to upload profile images"
   ON storage.objects FOR INSERT
   TO public
   WITH CHECK (bucket_id = 'profile-images');

   CREATE POLICY "Allow public to read profile images"
   ON storage.objects FOR SELECT
   TO public
   USING (bucket_id = 'profile-images');

   ```

3. **Enable Realtime:**
   - Go to Table Editor
   - Click on your `portfolio_comments` table
   - Click "Edit Table"
   - Enable "Realtime" in the table configuration

4. **Get API Keys:**
   - Go to Settings > API
   - Copy your project URL and anon public key

## 🔧 Environment Variables Setup

Create a `.env` file in your project root with both Firebase and Supabase configurations:

```env
# Firebase Configuration
VITE_FIREBASE_API_KEY=your-firebase-api-key
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_DATABASE_URL=https://your-project-default-rtdb.firebaseio.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=your-messaging-sender-id
VITE_FIREBASE_APP_ID=your-app-id

# Supabase Configuration
VITE_SUPABASE_URL=your-supabase-url
VITE_SUPABASE_ANON_KEY=your-supabase-anon-key
```

**Important:** 
- All environment variables must be prefixed with `VITE_` for Vite to access them
- Restart your development server after creating/modifying the `.env` file
- Never commit your `.env` file to version control (add it to `.gitignore`)

5. **Environment Variables Setup (Already covered above)**

6. **Configuration File:**
   The project uses `supabase-config-comment.js` with environment variables:
   ```javascript
   import { createClient } from '@supabase/supabase-js';

   // Access environment variables using import.meta.env for Vite
   const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
   const supabaseKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

   if (!supabaseUrl || !supabaseKey) {
     console.error("Supabase URL:", supabaseUrl);
     console.error("Supabase Anon Key:", supabaseKey);
     throw new Error("Supabase URL and Anon Key are required. Check your .env file and ensure they are prefixed with VITE_ and the dev server was restarted.");
   }

   export const supabase = createClient(supabaseUrl, supabaseKey);
   ```

   **Important:** Make sure to restart your dev server after creating/modifying the `.env` file.

## 🚨 Troubleshooting

If you encounter issues while running the project:

- Ensure Node.js is correctly installed
- Verify you're in the correct project directory
- Check that all dependencies are installed without errors
- Make sure your Firebase and Supabase configurations are correct
- Clear your browser cache and try again

## 📝 Usage & Credits

We would appreciate it if you decide to use this project. Please include proper credit when using it. Thank you! 🙏

## 📞 Contact

If you have any questions or need help with the setup, feel free to reach out!

**Eki Zulfar Rachman**
- Website: [https://www.eki.my.id/](https://www.eki.my.id/)
- GitHub: [EkiZR](https://github.com/EkiZR)

---

⭐ If this project helped you, please consider giving it a star on GitHub!
