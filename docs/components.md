# Documentation des Composants MAD

## Vue d'ensemble

Ce document décrit tous les composants du système MAD (Marketplace for Ads and Deals), leur responsabilité, leurs interactions et leur structure.

## Architecture Globale

L'application MAD suit une architecture en couches avec séparation claire des préoccupations :

```
┌─────────────────────────────────────────┐
│         Interface Utilisateur           │
│    (Frontend - React/Vue Components)    │
└─────────────────────────────────────────┘
                    ▼
┌─────────────────────────────────────────┐
│          Couche API (REST)              │
│        (Backend Controllers)            │
└─────────────────────────────────────────┘
                    ▼
┌─────────────────────────────────────────┐
│       Couche Métier (Services)          │
│    (Business Logic & Validation)        │
└─────────────────────────────────────────┘
                    ▼
┌─────────────────────────────────────────┐
│      Couche Données (Models/DB)         │
│        (PostgreSQL + Redis)             │
└─────────────────────────────────────────┘
```

## 1. Composants Frontend

### 1.1 Composants de Navigation

#### Header Component
**Responsabilité**: Barre de navigation principale
**Fonctionnalités**:
- Logo et nom de l'application
- Barre de recherche globale
- Menu de navigation (Accueil, Catégories, Mes Annonces)
- Boutons d'action (Publier une annonce, Connexion/Profil)
- Notifications badge

**Props**:
- `user`: Objet utilisateur connecté
- `notifications`: Nombre de notifications non lues
- `onSearch`: Callback pour la recherche

#### Navigation Menu
**Responsabilité**: Menu de navigation des catégories
**Fonctionnalités**:
- Liste des catégories principales
- Menu déroulant pour sous-catégories
- Liens rapides vers sections populaires

### 1.2 Composants d'Annonces

#### AdCard Component
**Responsabilité**: Affichage d'une annonce dans une liste
**Fonctionnalités**:
- Image principale de l'annonce
- Titre et prix
- Localisation et date de publication
- Badge "urgent" ou "professionnel"
- Icône favori

**Props**:
```typescript
interface AdCardProps {
  id: string;
  title: string;
  price: number;
  images: string[];
  location: string;
  publishedAt: Date;
  isUrgent: boolean;
  isPro: boolean;
  isFavorite: boolean;
  onFavoriteToggle: (id: string) => void;
}
```

#### AdDetail Component
**Responsabilité**: Page de détail complète d'une annonce
**Fonctionnalités**:
- Galerie d'images avec zoom
- Informations complètes de l'annonce
- Description détaillée
- Informations vendeur
- Boutons d'action (Contacter, Favoris, Signaler)
- Annonces similaires

#### AdForm Component
**Responsabilité**: Formulaire de création/modification d'annonce
**Fonctionnalités**:
- Sélection de catégorie
- Champs de saisie (titre, description, prix)
- Upload d'images (jusqu'à 10)
- Sélection de localisation
- Options d'urgence et type d'annonceur
- Prévisualisation

**Validation**:
- Titre: 10-100 caractères
- Description: 50-4000 caractères
- Prix: Nombre positif
- Images: Au moins 1, maximum 10

#### AdList Component
**Responsabilité**: Liste paginée d'annonces
**Fonctionnalités**:
- Affichage en grille ou liste
- Pagination
- Tri (date, prix, pertinence)
- Chargement infini (scroll)

### 1.3 Composants de Recherche et Filtrage

#### SearchBar Component
**Responsabilité**: Barre de recherche avec autocomplétion
**Fonctionnalités**:
- Recherche en temps réel
- Suggestions automatiques
- Historique de recherche
- Filtres rapides

#### FilterPanel Component
**Responsabilité**: Panneau de filtres avancés
**Fonctionnalités**:
- Filtre par catégorie
- Filtre par prix (min-max)
- Filtre par localisation (rayon)
- Filtre par date de publication
- Filtre par type d'annonceur
- Bouton réinitialiser

**État**:
```typescript
interface FilterState {
  category: string[];
  priceMin: number;
  priceMax: number;
  location: {
    city: string;
    radius: number;
  };
  dateRange: {
    from: Date;
    to: Date;
  };
  isUrgent: boolean;
  isPro: boolean;
}
```

### 1.4 Composants Utilisateur

#### UserProfile Component
**Responsabilité**: Page de profil utilisateur
**Fonctionnalités**:
- Informations personnelles
- Photo de profil
- Annonces actives
- Historique des annonces
- Statistiques (vues, favoris)

#### UserSettings Component
**Responsabilité**: Paramètres du compte
**Fonctionnalités**:
- Modification des informations
- Changement de mot de passe
- Préférences de notification
- Paramètres de confidentialité

#### AuthForms Component
**Responsabilité**: Formulaires d'authentification
**Sous-composants**:
- LoginForm
- RegisterForm
- ForgotPasswordForm
- ResetPasswordForm

### 1.5 Composants de Messagerie

#### MessageList Component
**Responsabilité**: Liste des conversations
**Fonctionnalités**:
- Liste des discussions
- Aperçu du dernier message
- Badge messages non lus
- Recherche de conversations

#### ChatWindow Component
**Responsabilité**: Fenêtre de conversation
**Fonctionnalités**:
- Historique des messages
- Zone de saisie
- Envoi de photos
- Indicateur "en train d'écrire"
- Informations de l'annonce concernée

### 1.6 Composants d'Administration

#### AdminDashboard Component
**Responsabilité**: Tableau de bord administrateur
**Fonctionnalités**:
- Statistiques globales
- Graphiques d'activité
- Annonces récentes à modérer
- Alertes système

#### ModerationPanel Component
**Responsabilité**: Panel de modération des annonces
**Fonctionnalités**:
- Liste des annonces à modérer
- Détails de l'annonce
- Actions (Approuver, Refuser, Supprimer)
- Historique de modération

## 2. Composants Backend

### 2.1 Contrôleurs (Controllers)

#### AuthController
**Responsabilité**: Gestion de l'authentification
**Endpoints**:
- POST `/api/auth/register` - Inscription
- POST `/api/auth/login` - Connexion
- POST `/api/auth/logout` - Déconnexion
- POST `/api/auth/refresh` - Rafraîchir le token
- POST `/api/auth/forgot-password` - Mot de passe oublié
- POST `/api/auth/reset-password` - Réinitialiser le mot de passe

#### AdController
**Responsabilité**: Gestion des annonces
**Endpoints**:
- GET `/api/ads` - Liste des annonces (avec filtres)
- GET `/api/ads/:id` - Détail d'une annonce
- POST `/api/ads` - Créer une annonce
- PUT `/api/ads/:id` - Modifier une annonce
- DELETE `/api/ads/:id` - Supprimer une annonce
- GET `/api/ads/:id/similar` - Annonces similaires

#### UserController
**Responsabilité**: Gestion des utilisateurs
**Endpoints**:
- GET `/api/users/me` - Profil de l'utilisateur connecté
- PUT `/api/users/me` - Modifier le profil
- GET `/api/users/:id` - Profil public d'un utilisateur
- GET `/api/users/me/ads` - Mes annonces
- GET `/api/users/me/favorites` - Mes favoris

#### MessageController
**Responsabilité**: Gestion de la messagerie
**Endpoints**:
- GET `/api/messages/conversations` - Liste des conversations
- GET `/api/messages/:conversationId` - Messages d'une conversation
- POST `/api/messages` - Envoyer un message
- PUT `/api/messages/:id/read` - Marquer comme lu

#### CategoryController
**Responsabilité**: Gestion des catégories
**Endpoints**:
- GET `/api/categories` - Liste des catégories
- GET `/api/categories/:id` - Détail d'une catégorie
- POST `/api/categories` - Créer une catégorie (admin)
- PUT `/api/categories/:id` - Modifier une catégorie (admin)

#### SearchController
**Responsabilité**: Recherche et filtrage
**Endpoints**:
- GET `/api/search` - Recherche d'annonces
- GET `/api/search/autocomplete` - Suggestions de recherche
- GET `/api/search/popular` - Recherches populaires

### 2.2 Services (Business Logic)

#### AuthService
**Responsabilité**: Logique d'authentification
**Méthodes**:
- `register(userData)` - Créer un compte utilisateur
- `login(email, password)` - Authentifier un utilisateur
- `verifyToken(token)` - Vérifier un JWT
- `hashPassword(password)` - Hasher un mot de passe
- `generateToken(userId)` - Générer un JWT
- `sendVerificationEmail(user)` - Envoyer email de vérification

#### AdService
**Responsabilité**: Logique métier des annonces
**Méthodes**:
- `createAd(adData, userId)` - Créer une annonce
- `updateAd(adId, adData, userId)` - Modifier une annonce
- `deleteAd(adId, userId)` - Supprimer une annonce
- `getAdsByFilters(filters)` - Récupérer annonces avec filtres
- `incrementViews(adId)` - Incrémenter les vues
- `getSimilarAds(adId)` - Trouver annonces similaires
- `validateAd(adData)` - Valider les données d'une annonce

#### MessageService
**Responsabilité**: Logique de messagerie
**Méthodes**:
- `createConversation(adId, fromUserId, toUserId)` - Créer une conversation
- `sendMessage(conversationId, userId, content)` - Envoyer un message
- `getConversations(userId)` - Récupérer les conversations
- `getMessages(conversationId, userId)` - Récupérer les messages
- `markAsRead(messageId, userId)` - Marquer comme lu

#### SearchService
**Responsabilité**: Recherche et indexation
**Méthodes**:
- `searchAds(query, filters)` - Rechercher des annonces
- `indexAd(ad)` - Indexer une annonce
- `getSuggestions(query)` - Obtenir des suggestions
- `updateSearchRanking(adId)` - Mettre à jour le ranking

#### NotificationService
**Responsabilité**: Gestion des notifications
**Méthodes**:
- `sendNotification(userId, type, data)` - Envoyer une notification
- `getNotifications(userId)` - Récupérer les notifications
- `markAsRead(notificationId)` - Marquer comme lue
- `sendEmailNotification(userId, template, data)` - Envoyer email

#### ImageService
**Responsabilité**: Gestion des images
**Méthodes**:
- `uploadImage(file)` - Upload une image
- `resizeImage(file, sizes)` - Redimensionner une image
- `deleteImage(imageId)` - Supprimer une image
- `optimizeImage(file)` - Optimiser une image

### 2.3 Modèles (Models)

#### User Model
**Attributs**:
```typescript
{
  id: UUID;
  email: string;
  password: string; // hashé
  firstName: string;
  lastName: string;
  phone: string;
  avatar: string;
  isVerified: boolean;
  isPro: boolean;
  createdAt: Date;
  updatedAt: Date;
}
```

#### Ad Model
**Attributs**:
```typescript
{
  id: UUID;
  userId: UUID;
  categoryId: UUID;
  title: string;
  description: string;
  price: number;
  images: string[];
  location: {
    city: string;
    zipCode: string;
    coordinates: {
      lat: number;
      lng: number;
    }
  };
  isUrgent: boolean;
  status: 'draft' | 'published' | 'sold' | 'expired';
  views: number;
  publishedAt: Date;
  expiresAt: Date;
  createdAt: Date;
  updatedAt: Date;
}
```

#### Category Model
**Attributs**:
```typescript
{
  id: UUID;
  name: string;
  slug: string;
  icon: string;
  parentId: UUID | null;
  order: number;
  createdAt: Date;
}
```

#### Message Model
**Attributs**:
```typescript
{
  id: UUID;
  conversationId: UUID;
  senderId: UUID;
  content: string;
  attachments: string[];
  isRead: boolean;
  createdAt: Date;
}
```

#### Conversation Model
**Attributs**:
```typescript
{
  id: UUID;
  adId: UUID;
  participantIds: UUID[];
  lastMessageAt: Date;
  createdAt: Date;
}
```

#### Favorite Model
**Attributs**:
```typescript
{
  id: UUID;
  userId: UUID;
  adId: UUID;
  createdAt: Date;
}
```

### 2.4 Middleware

#### AuthMiddleware
**Responsabilité**: Vérification de l'authentification
**Fonctionnalités**:
- Vérifier le token JWT
- Extraire l'utilisateur du token
- Bloquer l'accès si non authentifié

#### ValidationMiddleware
**Responsabilité**: Validation des données
**Fonctionnalités**:
- Valider les données d'entrée
- Sanitiser les données
- Retourner erreurs de validation

#### RateLimitMiddleware
**Responsabilité**: Limitation du taux de requêtes
**Fonctionnalités**:
- Limiter le nombre de requêtes par IP
- Protéger contre les abus
- Retourner erreur 429 si limite dépassée

#### ErrorHandlerMiddleware
**Responsabilité**: Gestion centralisée des erreurs
**Fonctionnalités**:
- Capturer toutes les erreurs
- Formater les réponses d'erreur
- Logger les erreurs

## 3. Composants d'Infrastructure

### 3.1 Base de Données

#### PostgreSQL
**Tables principales**:
- users
- ads
- categories
- messages
- conversations
- favorites
- notifications
- reports

#### Redis
**Utilisation**:
- Cache des sessions
- Cache des résultats de recherche
- Rate limiting
- Pub/Sub pour notifications temps réel

### 3.2 Services Externes

#### Service de Stockage (AWS S3 / Cloudflare)
**Responsabilité**: Stockage des images et fichiers
**Fonctionnalités**:
- Upload d'images
- CDN pour distribution
- Backup automatique

#### Service de Géolocalisation
**Responsabilité**: Localisation et calcul de distances
**Fonctionnalités**:
- Géocodage d'adresses
- Calcul de distance entre points
- Recherche par rayon

#### Service d'Email (SendGrid / Mailgun)
**Responsabilité**: Envoi d'emails
**Fonctionnalités**:
- Emails transactionnels
- Emails de notification
- Newsletters

## 4. Flux de Données Principaux

### 4.1 Publication d'une Annonce
```
User → AdForm → Validation → ImageService → AdService → Database → SearchService (indexation)
```

### 4.2 Recherche d'Annonces
```
User → SearchBar → SearchService → Cache (Redis) → Database → AdList → User
```

### 4.3 Messagerie
```
User → ChatWindow → MessageService → WebSocket → Database → Notification → TargetUser
```

### 4.4 Authentification
```
User → LoginForm → AuthController → AuthService → Database → JWT Token → User
```

## 5. Sécurité

### Mesures de Sécurité Implémentées
- Hashage des mots de passe (bcrypt)
- JWT pour authentification
- HTTPS obligatoire
- CORS configuré
- Protection CSRF
- Rate limiting
- Validation et sanitisation des entrées
- Protection contre injection SQL
- Protection XSS
- Headers de sécurité (Helmet.js)

## 6. Performance

### Optimisations
- Cache Redis pour requêtes fréquentes
- Indexation base de données
- Lazy loading des images
- Pagination des résultats
- CDN pour assets statiques
- Compression gzip
- Minification CSS/JS

## Conclusion

Cette architecture modulaire permet une maintenance facile, une scalabilité et une évolution du projet. Chaque composant a une responsabilité claire et les interactions sont bien définies.
