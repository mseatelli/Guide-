# Guide+ 🗺️

Application web collaborative sur invitation pour partager et découvrir les meilleurs lieux (restaurants, cafés, hôtels, activités, etc.).

![Guide+](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)

## ✨ Fonctionnalités

### Pour tous les utilisateurs
- 🔐 **Authentification sécurisée** (inscription sur invitation uniquement)
- 📍 **7 catégories de lieux** : Restaurants, Cafés & bars, Hôtels, Lieux à découvrir, Activités, Spectacles, Shopping
- ⭐ **Notation** de 1 à 5 étoiles
- 💬 **Commentaires** avec système de réponses
- 📤 **Partage natif** (WhatsApp, SMS, etc.)
- 🌓 **Mode clair/sombre**
- 📱 **Responsive** (mobile, tablette, desktop)
- 🔄 **Synchronisation en temps réel** entre tous les utilisateurs

### Pour les administrateurs
- 👑 **Panel admin** complet
- 📊 **Statistiques** (utilisateurs, lieux, commentaires)
- 🎟️ **Génération de codes d'invitation** avec copie automatique
- 👥 **Gestion des utilisateurs**

## 🛠️ Technologies

- **Frontend** : HTML5, CSS3, JavaScript (Vanilla)
- **Backend** : Supabase
- **Base de données** : PostgreSQL (via Supabase)
- **Authentification** : Supabase Auth
- **Hébergement** : GitHub Pages

## 📦 Installation

### Prérequis
- Un compte [Supabase](https://supabase.com) (gratuit)
- Un compte GitHub

### 1. Créer le projet Supabase

1. Créez un nouveau projet sur [Supabase](https://supabase.com)
2. Dans le **SQL Editor**, exécutez ce script :
```sql
-- Créer les tables
CREATE TABLE themes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    icon TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE user_profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    user_name TEXT NOT NULL,
    display_name TEXT NOT NULL,
    role TEXT DEFAULT 'user' CHECK (role IN ('user', 'admin')),
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE posts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    theme_id UUID NOT NULL REFERENCES themes(id) ON DELETE CASCADE,
    title TEXT NOT NULL,
    content TEXT,
    rating INTEGER CHECK (rating >= 1 AND rating <= 5),
    author_profile TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE comments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    author_name TEXT NOT NULL,
    content TEXT NOT NULL,
    parent_comment_id UUID REFERENCES comments(id) ON DELETE CASCADE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE invitations (
    id TEXT PRIMARY KEY,
    message TEXT,
    sent_at TIMESTAMPTZ DEFAULT NOW()
);

-- Insérer les thèmes par défaut
INSERT INTO themes (name, icon) VALUES
    ('Restaurants', '🍽️'),
    ('Cafés et bars', '☕'),
    ('Hôtels', '🏨'),
    ('Lieux à découvrir', '📍'),
    ('Activités', '🎯'),
    ('Spectacles', '🎭'),
    ('Shopping', '🛍️');

-- Activer Row Level Security
ALTER TABLE themes ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
ALTER TABLE comments ENABLE ROW LEVEL SECURITY;
ALTER TABLE invitations ENABLE ROW LEVEL SECURITY;

-- Policies (lecture publique pour tous)
CREATE POLICY "Public read" ON themes FOR SELECT USING (true);
CREATE POLICY "Public read" ON user_profiles FOR SELECT USING (true);
CREATE POLICY "Public read" ON posts FOR SELECT USING (true);
CREATE POLICY "Public read" ON comments FOR SELECT USING (true);
CREATE POLICY "Public read" ON invitations FOR SELECT USING (true);

-- Policies d'écriture
CREATE POLICY "Users can insert" ON user_profiles FOR INSERT WITH CHECK (auth.uid() = id);
CREATE POLICY "Authenticated can insert" ON posts FOR INSERT WITH CHECK (auth.role() = 'authenticated');
CREATE POLICY "Authenticated can insert" ON comments FOR INSERT WITH CHECK (auth.role() = 'authenticated');
```

3. Allez dans **Authentication → Providers**
4. Activez **Email provider** et désactivez **Confirm email**
5. Auth Providers, activez "Email".

### 2. Créer votre compte admin

1. Dans **Authentication → Users**, créez un utilisateur avec **"Auto Confirm User"** coché
2. Copiez l'**UID** de l'utilisateur créé
3. Dans **Table Editor → user_profiles**, insérez une ligne :
   - `id` : l'UID copié
   - `user_name` : votre nom
   - `display_name` : votre nom d'affichage
   - `role` : `admin`

### 3. Configurer l'application

1. Récupérez vos identifiants Supabase :
   - **Project URL** : Settings → API → Project URL
   - **Anon key** : Settings → API → anon/public key

2. Modifiez le fichier `index.html` (lignes 706-710) :
```javascript
this.sb = createClient(
    'VOTRE_SUPABASE_URL',
    'VOTRE_SUPABASE_ANON_KEY'
);
```

### 4. Déployer sur GitHub Pages

1. Créez un nouveau repository sur GitHub
2. Uploadez le fichier `index.html`
3. Allez dans **Settings → Pages**
4. Source : **Deploy from a branch**
5. Branch : **main** / Folder : **/ (root)**
6. Cliquez sur **Save**

Votre application sera accessible à : `https://VOTRE-USERNAME.github.io/VOTRE-REPO/`

## 📱 Utilisation

### Pour les utilisateurs

1. Recevez un **code d'invitation** d'un administrateur
2. Créez votre compte avec le code
3. Ajoutez vos lieux préférés avec notes et descriptions
4. Commentez les posts des autres
5. Partagez vos découvertes via WhatsApp
6. Marquez des favoris
7. Contactez l'administrateur

### Pour les administrateurs

1. Connectez-vous avec votre compte admin
2. Cliquez sur **👑 Admin** en haut à droite
3. Générez des codes d'invitation (copie automatique)
4. Gérez les utilisateurs
5. Consultez les statistiques
6. Créez des annonces

## 🔒 Sécurité

- Authentification par Supabase Auth
- Inscription sur invitation uniquement
- Row Level Security (RLS) activé
- Données chiffrées en transit (HTTPS)

## 📄 License

MIT License - Vous êtes libre d'utiliser, modifier et distribuer ce projet.

## 🤝 Contribution

Les contributions sont les bienvenues ! N'hésitez pas à ouvrir une issue ou une pull request.

## 📧 Contact

Pour toute question : [mseatelli@gmail.com]

---

Fait par [Marc Seatelli]
