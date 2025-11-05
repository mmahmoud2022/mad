# Documentation API MAD

## Introduction

Cette documentation décrit l'API REST du projet MAD (Marketplace for Ads and Deals). L'API permet d'interagir avec la plateforme de petites annonces pour gérer les utilisateurs, les annonces, les messages, et plus encore.

## Base URL

```
Development: http://localhost:3000/api
Production:  https://api.mad.com/api
```

## Authentification

L'API utilise JWT (JSON Web Tokens) pour l'authentification.

### Obtenir un Token

```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

**Réponse:**
```json
{
  "success": true,
  "data": {
    "token": "your_jwt_token_here",
    "refreshToken": "your_refresh_token_here",
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe"
    }
  }
}
```

### Utiliser le Token

Inclure le token dans l'en-tête `Authorization` de chaque requête:

```http
Authorization: Bearer your_jwt_token_here
```

## Format de Réponse

Toutes les réponses API suivent ce format standard:

### Succès
```json
{
  "success": true,
  "data": { ... },
  "message": "Operation successful"
}
```

### Erreur
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error message",
    "details": { ... }
  }
}
```

## Codes d'Erreur

| Code | Description |
|------|-------------|
| 200 | OK - Succès |
| 201 | Created - Ressource créée |
| 204 | No Content - Succès sans contenu |
| 400 | Bad Request - Requête invalide |
| 401 | Unauthorized - Non authentifié |
| 403 | Forbidden - Non autorisé |
| 404 | Not Found - Ressource non trouvée |
| 409 | Conflict - Conflit (ex: email existe) |
| 422 | Unprocessable Entity - Validation échouée |
| 429 | Too Many Requests - Rate limit dépassé |
| 500 | Internal Server Error - Erreur serveur |

## Endpoints

### Authentication

#### Inscription

```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+33612345678"
}
```

**Réponse (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe"
    },
    "token": "jwt_token"
  }
}
```

#### Connexion

```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123!"
}
```

#### Déconnexion

```http
POST /api/auth/logout
Authorization: Bearer {token}
```

#### Rafraîchir le Token

```http
POST /api/auth/refresh
Content-Type: application/json

{
  "refreshToken": "refresh_token_here"
}
```

#### Mot de Passe Oublié

```http
POST /api/auth/forgot-password
Content-Type: application/json

{
  "email": "user@example.com"
}
```

#### Réinitialiser le Mot de Passe

```http
POST /api/auth/reset-password
Content-Type: application/json

{
  "token": "reset_token",
  "password": "NewSecurePass123!"
}
```

### Annonces (Ads)

#### Lister les Annonces

```http
GET /api/ads?page=1&limit=20&category=electronics&city=Paris&priceMin=10&priceMax=1000
```

**Paramètres de Requête:**
- `page` (integer): Numéro de page (défaut: 1)
- `limit` (integer): Nombre de résultats (défaut: 20, max: 100)
- `category` (string): Slug de catégorie
- `city` (string): Ville
- `priceMin` (number): Prix minimum
- `priceMax` (number): Prix maximum
- `sort` (string): Tri (date, price-asc, price-desc)
- `search` (string): Recherche textuelle
- `isUrgent` (boolean): Annonces urgentes uniquement

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "ads": [
      {
        "id": "uuid",
        "title": "iPhone 13 Pro",
        "description": "Excellent état, avec boîte",
        "price": 699.99,
        "images": ["url1", "url2"],
        "location": {
          "city": "Paris",
          "zipCode": "75001"
        },
        "category": {
          "id": "uuid",
          "name": "Téléphones",
          "slug": "phones"
        },
        "user": {
          "id": "uuid",
          "firstName": "John",
          "isPro": false
        },
        "isUrgent": false,
        "views": 123,
        "publishedAt": "2024-01-01T12:00:00Z",
        "createdAt": "2024-01-01T12:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 156,
      "totalPages": 8
    }
  }
}
```

#### Obtenir une Annonce

```http
GET /api/ads/:id
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "iPhone 13 Pro",
    "description": "Excellent état, avec boîte et chargeur",
    "price": 699.99,
    "images": ["url1", "url2", "url3"],
    "location": {
      "city": "Paris",
      "zipCode": "75001",
      "coordinates": {
        "lat": 48.8566,
        "lng": 2.3522
      }
    },
    "category": {
      "id": "uuid",
      "name": "Téléphones",
      "slug": "phones"
    },
    "user": {
      "id": "uuid",
      "firstName": "John",
      "lastName": "D.",
      "avatar": "avatar_url",
      "isPro": false,
      "memberSince": "2023-01-01T00:00:00Z"
    },
    "isUrgent": false,
    "status": "published",
    "views": 123,
    "publishedAt": "2024-01-01T12:00:00Z",
    "expiresAt": "2024-03-01T12:00:00Z",
    "createdAt": "2024-01-01T12:00:00Z",
    "updatedAt": "2024-01-01T12:00:00Z"
  }
}
```

#### Créer une Annonce

```http
POST /api/ads
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "iPhone 13 Pro",
  "description": "Excellent état, avec boîte et chargeur. Acheté il y a 6 mois.",
  "price": 699.99,
  "categoryId": "uuid",
  "images": ["base64_or_url1", "base64_or_url2"],
  "location": {
    "city": "Paris",
    "zipCode": "75001"
  },
  "isUrgent": false
}
```

**Réponse (201):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "iPhone 13 Pro",
    "status": "published",
    "publishedAt": "2024-01-01T12:00:00Z"
  },
  "message": "Annonce créée avec succès"
}
```

#### Modifier une Annonce

```http
PUT /api/ads/:id
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "iPhone 13 Pro - Prix baissé",
  "price": 649.99,
  "description": "Excellent état..."
}
```

#### Supprimer une Annonce

```http
DELETE /api/ads/:id
Authorization: Bearer {token}
```

**Réponse (204):** No Content

#### Obtenir Annonces Similaires

```http
GET /api/ads/:id/similar?limit=5
```

#### Incrémenter les Vues

```http
POST /api/ads/:id/views
```

### Utilisateurs (Users)

#### Mon Profil

```http
GET /api/users/me
Authorization: Bearer {token}
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+33612345678",
    "avatar": "avatar_url",
    "isVerified": true,
    "isPro": false,
    "createdAt": "2023-01-01T00:00:00Z",
    "stats": {
      "adsCount": 5,
      "soldCount": 12,
      "favoriteCount": 8
    }
  }
}
```

#### Modifier mon Profil

```http
PUT /api/users/me
Authorization: Bearer {token}
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+33612345678",
  "avatar": "base64_or_url"
}
```

#### Profil Public d'un Utilisateur

```http
GET /api/users/:id
```

#### Mes Annonces

```http
GET /api/users/me/ads?status=published&page=1&limit=10
```

**Paramètres:**
- `status` (string): published, sold, expired, draft

#### Mes Favoris

```http
GET /api/users/me/favorites?page=1&limit=20
```

#### Ajouter aux Favoris

```http
POST /api/users/me/favorites/:adId
Authorization: Bearer {token}
```

**Réponse (201):**
```json
{
  "success": true,
  "message": "Annonce ajoutée aux favoris"
}
```

#### Retirer des Favoris

```http
DELETE /api/users/me/favorites/:adId
Authorization: Bearer {token}
```

### Messages

#### Liste des Conversations

```http
GET /api/messages/conversations?page=1&limit=20
Authorization: Bearer {token}
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "conversations": [
      {
        "id": "uuid",
        "ad": {
          "id": "uuid",
          "title": "iPhone 13 Pro",
          "image": "url",
          "price": 699.99
        },
        "otherUser": {
          "id": "uuid",
          "firstName": "Jane",
          "avatar": "url"
        },
        "lastMessage": {
          "content": "Est-ce toujours disponible?",
          "sentAt": "2024-01-01T14:30:00Z",
          "isRead": false
        },
        "unreadCount": 2
      }
    ]
  }
}
```

#### Messages d'une Conversation

```http
GET /api/messages/:conversationId?page=1&limit=50
Authorization: Bearer {token}
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "messages": [
      {
        "id": "uuid",
        "senderId": "uuid",
        "content": "Bonjour, est-ce toujours disponible?",
        "attachments": [],
        "isRead": true,
        "createdAt": "2024-01-01T14:30:00Z"
      },
      {
        "id": "uuid",
        "senderId": "uuid",
        "content": "Oui, c'est disponible!",
        "attachments": [],
        "isRead": false,
        "createdAt": "2024-01-01T14:32:00Z"
      }
    ]
  }
}
```

#### Envoyer un Message

```http
POST /api/messages
Authorization: Bearer {token}
Content-Type: application/json

{
  "conversationId": "uuid",
  "content": "Je suis intéressé par votre annonce",
  "attachments": ["url1"]
}
```

**Pour un premier message (nouvelle conversation):**
```json
{
  "adId": "uuid",
  "receiverId": "uuid",
  "content": "Bonjour, est-ce toujours disponible?"
}
```

#### Marquer comme Lu

```http
PUT /api/messages/:id/read
Authorization: Bearer {token}
```

### Catégories

#### Liste des Catégories

```http
GET /api/categories
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "categories": [
      {
        "id": "uuid",
        "name": "Électronique",
        "slug": "electronique",
        "icon": "icon_url",
        "children": [
          {
            "id": "uuid",
            "name": "Téléphones",
            "slug": "phones",
            "icon": "icon_url"
          },
          {
            "id": "uuid",
            "name": "Ordinateurs",
            "slug": "computers",
            "icon": "icon_url"
          }
        ]
      }
    ]
  }
}
```

#### Détail d'une Catégorie

```http
GET /api/categories/:id
```

### Recherche

#### Recherche d'Annonces

```http
GET /api/search?q=iphone&category=electronics&city=Paris&page=1
```

**Paramètres:**
- `q` (string): Terme de recherche
- `category` (string): Slug de catégorie
- `city` (string): Ville
- `priceMin`, `priceMax` (number): Fourchette de prix
- `sort` (string): relevance, date, price-asc, price-desc

#### Autocomplétion

```http
GET /api/search/autocomplete?q=iph
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "suggestions": [
      "iPhone 13",
      "iPhone 14",
      "iPhone Pro Max"
    ]
  }
}
```

#### Recherches Populaires

```http
GET /api/search/popular?limit=10
```

## WebSocket API

### Connexion

```javascript
const socket = io('wss://api.mad.com', {
  auth: {
    token: 'jwt_token'
  }
});
```

### Événements Client → Serveur

#### Envoyer un Message

```javascript
socket.emit('message:send', {
  conversationId: 'uuid',
  content: 'Bonjour!'
});
```

#### Indiquer que l'Utilisateur Écrit

```javascript
socket.emit('typing:start', {
  conversationId: 'uuid'
});

socket.emit('typing:stop', {
  conversationId: 'uuid'
});
```

### Événements Serveur → Client

#### Message Reçu

```javascript
socket.on('message:received', (data) => {
  // {
  //   conversationId: 'uuid',
  //   message: { ... }
  // }
});
```

#### Quelqu'un Écrit

```javascript
socket.on('typing', (data) => {
  // {
  //   conversationId: 'uuid',
  //   userId: 'uuid'
  // }
});
```

#### Notification

```javascript
socket.on('notification', (data) => {
  // {
  //   type: 'new_message' | 'ad_sold' | 'ad_expired',
  //   data: { ... }
  // }
});
```

## Rate Limiting

L'API applique des limites de taux pour prévenir les abus:

| Endpoint | Limite |
|----------|--------|
| Auth (login, register) | 5 requêtes / 15 minutes |
| Création d'annonces | 10 annonces / heure |
| Messages | 60 messages / heure |
| Recherche | 100 requêtes / minute |
| Général | 1000 requêtes / heure |

**En-têtes de réponse:**
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

## Pagination

Toutes les listes utilisent une pagination basée sur les pages:

**Requête:**
```
GET /api/ads?page=2&limit=20
```

**Réponse:**
```json
{
  "data": [...],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 156,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": true
  }
}
```

## Upload de Fichiers

### Upload d'Image

```http
POST /api/upload/image
Authorization: Bearer {token}
Content-Type: multipart/form-data

file: (binary)
```

**Réponse (200):**
```json
{
  "success": true,
  "data": {
    "url": "https://cdn.mad.com/images/uuid.jpg",
    "thumbnailUrl": "https://cdn.mad.com/images/uuid_thumb.jpg"
  }
}
```

**Limites:**
- Taille max: 5MB
- Formats: JPEG, PNG, WebP
- Dimensions max: 4096x4096

## Webhooks (Admin)

Pour les intégrations, MAD peut envoyer des webhooks pour certains événements:

### Configuration

```http
POST /api/admin/webhooks
Authorization: Bearer {admin_token}
Content-Type: application/json

{
  "url": "https://your-app.com/webhook",
  "events": ["ad.created", "ad.sold", "user.registered"]
}
```

### Événements Disponibles

- `ad.created`: Nouvelle annonce créée
- `ad.updated`: Annonce modifiée
- `ad.sold`: Annonce vendue
- `ad.expired`: Annonce expirée
- `user.registered`: Nouvel utilisateur
- `message.sent`: Message envoyé

### Payload Webhook

```json
{
  "event": "ad.created",
  "timestamp": "2024-01-01T12:00:00Z",
  "data": {
    "adId": "uuid",
    "userId": "uuid",
    "title": "iPhone 13 Pro"
  }
}
```

## Environnements

### Development
```
Base URL: http://localhost:3000/api
WebSocket: ws://localhost:3000
```

### Staging
```
Base URL: https://staging-api.mad.com/api
WebSocket: wss://staging-api.mad.com
```

### Production
```
Base URL: https://api.mad.com/api
WebSocket: wss://api.mad.com
```

## Support

Pour toute question sur l'API:
- Documentation: https://docs.mad.com
- Email: api-support@mad.com
- Issues: https://github.com/mmahmoud2022/mad/issues
