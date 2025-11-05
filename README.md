# MAD - Marketplace for Ads and Deals

MAD est un projet de plateforme de petites annonces inspirée de leboncoin.fr, permettant aux utilisateurs de publier, rechercher et gérer des annonces de vente, achat, location et services.

## Description du Projet

MAD (Marketplace for Ads and Deals) est une application web moderne de petites annonces permettant de mettre en relation acheteurs et vendeurs pour divers types de produits et services.

## Fonctionnalités Principales

### Pour les Utilisateurs
- Publication d'annonces (vente, achat, location, services)
- Recherche et filtrage avancés des annonces
- Messagerie intégrée entre utilisateurs
- Gestion de profil utilisateur
- Système de favoris
- Historique des annonces
- Notifications en temps réel

### Pour les Administrateurs
- Modération des annonces
- Gestion des utilisateurs
- Statistiques et analytics
- Gestion des catégories
- Système de signalement

## Architecture du Projet

Le projet MAD suit une architecture modulaire moderne avec les composants suivants :

### 1. Frontend (Client Web)
- **Framework**: React.js / Vue.js
- **UI Components**: Composants réutilisables
- **State Management**: Redux / Vuex
- **Routing**: React Router / Vue Router

### 2. Backend (API REST)
- **Framework**: Node.js + Express / Python + Django / Java + Spring Boot
- **API**: RESTful API
- **Authentication**: JWT
- **Base de données**: PostgreSQL / MongoDB

### 3. Services
- **Service d'authentification**: Gestion des utilisateurs et sessions
- **Service d'annonces**: CRUD des annonces
- **Service de messagerie**: Communication entre utilisateurs
- **Service de recherche**: Indexation et recherche avancée
- **Service de notifications**: Notifications temps réel
- **Service de modération**: Validation des annonces

## Stack Technique

### Frontend
- **React.js** ou **Vue.js** : Framework JavaScript
- **TypeScript** : Typage statique
- **Tailwind CSS** : Styling
- **Axios** : HTTP client
- **Socket.io** : WebSocket pour temps réel

### Backend
- **Node.js + Express** : Serveur API
- **PostgreSQL** : Base de données relationnelle
- **Redis** : Cache et sessions
- **JWT** : Authentification
- **Multer** : Upload de fichiers

### DevOps
- **Docker** : Containerisation
- **GitHub Actions** : CI/CD
- **Nginx** : Reverse proxy
- **AWS / Azure / GCP** : Cloud hosting

## Structure des Répertoires

```
mad/
├── docs/                    # Documentation
│   ├── architecture.md      # Architecture détaillée
│   ├── api.md              # Documentation API
│   └── components.md       # Documentation des composants
├── frontend/               # Application cliente
│   ├── src/
│   │   ├── components/     # Composants React/Vue
│   │   ├── pages/          # Pages de l'application
│   │   ├── services/       # Services API
│   │   ├── store/          # State management
│   │   └── utils/          # Utilitaires
│   └── public/             # Fichiers statiques
├── backend/                # API serveur
│   ├── src/
│   │   ├── controllers/    # Contrôleurs
│   │   ├── models/         # Modèles de données
│   │   ├── routes/         # Routes API
│   │   ├── middleware/     # Middlewares
│   │   ├── services/       # Logique métier
│   │   └── utils/          # Utilitaires
│   └── tests/              # Tests unitaires et d'intégration
├── database/               # Scripts et migrations DB
│   ├── migrations/         # Migrations de schéma
│   └── seeds/              # Données de test
├── docker/                 # Configuration Docker
├── scripts/                # Scripts utilitaires
└── README.md              # Ce fichier
```

## Installation et Configuration

### Prérequis
- Node.js (v16+)
- PostgreSQL (v13+)
- Redis (v6+)
- Docker (optionnel)

### Installation

```bash
# Cloner le repository
git clone https://github.com/mmahmoud2022/mad.git
cd mad

# Installer les dépendances backend
cd backend
npm install

# Installer les dépendances frontend
cd ../frontend
npm install

# Configuration
cp .env.example .env
# Éditer .env avec vos paramètres
```

### Lancement

```bash
# Développement
npm run dev

# Production
npm run build
npm start
```

## Contribution

Les contributions sont les bienvenues ! Merci de lire le guide de contribution avant de soumettre une pull request.

## Licence

MIT License

## Contact

Pour toute question ou suggestion, veuillez ouvrir une issue sur GitHub.