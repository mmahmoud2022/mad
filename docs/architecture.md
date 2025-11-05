# Architecture du Projet MAD

## Vue d'ensemble

MAD (Marketplace for Ads and Deals) est une application de petites annonces construite sur une architecture moderne, scalable et maintenable. Ce document décrit l'architecture technique globale du système.

## Architecture Générale

### Architecture en Couches

```
┌──────────────────────────────────────────────────────────────┐
│                    Couche Présentation                        │
│                   (Frontend Web & Mobile)                     │
│                                                               │
│  ┌─────────────┐  ┌─────────────┐  ┌──────────────────┐    │
│  │   React/    │  │    Redux    │  │   Components     │    │
│  │   Vue.js    │  │    Store    │  │   Library        │    │
│  └─────────────┘  └─────────────┘  └──────────────────┘    │
└──────────────────────────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                      Couche API Gateway                       │
│                      (API REST + WebSocket)                   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Express.js / Fastify                     │   │
│  │  • Routing                                            │   │
│  │  • Authentication (JWT)                               │   │
│  │  • Rate Limiting                                      │   │
│  │  • Request Validation                                 │   │
│  └──────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                     Couche Services Métier                    │
│                    (Business Logic Layer)                     │
│                                                               │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │     Ad      │ │    User     │ │     Message         │   │
│  │   Service   │ │   Service   │ │     Service         │   │
│  └─────────────┘ └─────────────┘ └─────────────────────┘   │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │   Search    │ │    Auth     │ │   Notification      │   │
│  │   Service   │ │   Service   │ │     Service         │   │
│  └─────────────┘ └─────────────┘ └─────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                      Couche Persistance                       │
│                    (Data Access Layer)                        │
│                                                               │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────┐   │
│  │  PostgreSQL  │  │    Redis     │  │   Elasticsearch │   │
│  │   (Primary)  │  │   (Cache)    │  │    (Search)     │   │
│  └──────────────┘  └──────────────┘  └─────────────────┘   │
└──────────────────────────────────────────────────────────────┘
```

## Architecture Détaillée

### 1. Frontend Architecture

#### Structure des Composants

```
frontend/
├── src/
│   ├── app/
│   │   ├── App.tsx                 # Composant principal
│   │   ├── routes.tsx              # Configuration des routes
│   │   └── store.ts                # Configuration Redux/Vuex
│   │
│   ├── components/
│   │   ├── common/                 # Composants réutilisables
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Modal/
│   │   │   └── Card/
│   │   ├── layout/                 # Composants de mise en page
│   │   │   ├── Header/
│   │   │   ├── Footer/
│   │   │   └── Sidebar/
│   │   └── features/               # Composants métier
│   │       ├── ads/
│   │       ├── users/
│   │       └── messages/
│   │
│   ├── pages/                      # Pages de l'application
│   │   ├── Home/
│   │   ├── AdList/
│   │   ├── AdDetail/
│   │   ├── CreateAd/
│   │   ├── Profile/
│   │   └── Messages/
│   │
│   ├── services/                   # Services API
│   │   ├── api.ts                  # Client HTTP
│   │   ├── adService.ts
│   │   ├── userService.ts
│   │   └── messageService.ts
│   │
│   ├── store/                      # State management
│   │   ├── slices/
│   │   │   ├── authSlice.ts
│   │   │   ├── adSlice.ts
│   │   │   └── uiSlice.ts
│   │   └── middleware/
│   │
│   ├── hooks/                      # Custom hooks
│   │   ├── useAuth.ts
│   │   ├── useAds.ts
│   │   └── useWebSocket.ts
│   │
│   ├── utils/                      # Utilitaires
│   │   ├── validation.ts
│   │   ├── formatters.ts
│   │   └── constants.ts
│   │
│   └── assets/                     # Assets statiques
│       ├── images/
│       ├── icons/
│       └── styles/
│
└── public/
    └── index.html
```

#### State Management Pattern

```typescript
// Store structure
{
  auth: {
    user: User | null,
    token: string | null,
    isAuthenticated: boolean
  },
  ads: {
    list: Ad[],
    currentAd: Ad | null,
    filters: FilterState,
    loading: boolean,
    error: string | null
  },
  messages: {
    conversations: Conversation[],
    currentConversation: Conversation | null,
    unreadCount: number
  },
  ui: {
    sidebarOpen: boolean,
    modalOpen: boolean,
    theme: 'light' | 'dark'
  }
}
```

### 2. Backend Architecture

#### Structure des Fichiers

```
backend/
├── src/
│   ├── config/                     # Configuration
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   ├── jwt.ts
│   │   └── env.ts
│   │
│   ├── controllers/                # Contrôleurs
│   │   ├── authController.ts
│   │   ├── adController.ts
│   │   ├── userController.ts
│   │   └── messageController.ts
│   │
│   ├── services/                   # Services métier
│   │   ├── authService.ts
│   │   ├── adService.ts
│   │   ├── userService.ts
│   │   ├── messageService.ts
│   │   ├── searchService.ts
│   │   ├── notificationService.ts
│   │   └── imageService.ts
│   │
│   ├── models/                     # Modèles de données
│   │   ├── User.ts
│   │   ├── Ad.ts
│   │   ├── Category.ts
│   │   ├── Message.ts
│   │   └── Conversation.ts
│   │
│   ├── routes/                     # Routes API
│   │   ├── index.ts
│   │   ├── authRoutes.ts
│   │   ├── adRoutes.ts
│   │   ├── userRoutes.ts
│   │   └── messageRoutes.ts
│   │
│   ├── middleware/                 # Middleware
│   │   ├── auth.ts
│   │   ├── validation.ts
│   │   ├── errorHandler.ts
│   │   ├── rateLimit.ts
│   │   └── logger.ts
│   │
│   ├── utils/                      # Utilitaires
│   │   ├── logger.ts
│   │   ├── validator.ts
│   │   ├── helpers.ts
│   │   └── constants.ts
│   │
│   ├── types/                      # Types TypeScript
│   │   ├── express.d.ts
│   │   └── models.ts
│   │
│   └── app.ts                      # Point d'entrée
│
├── tests/                          # Tests
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── package.json
```

#### Modèle de Requête-Réponse

```
Client Request
    ↓
┌─────────────────┐
│   Middleware    │
│  • Auth Check   │
│  • Validation   │
│  • Rate Limit   │
└─────────────────┘
    ↓
┌─────────────────┐
│   Controller    │
│  • Parse Input  │
│  • Call Service │
└─────────────────┘
    ↓
┌─────────────────┐
│    Service      │
│  • Business     │
│    Logic        │
│  • Validation   │
└─────────────────┘
    ↓
┌─────────────────┐
│     Model       │
│  • Database     │
│    Operations   │
└─────────────────┘
    ↓
Response to Client
```

### 3. Base de Données

#### Schéma PostgreSQL

```sql
-- Users Table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    avatar VARCHAR(500),
    is_verified BOOLEAN DEFAULT FALSE,
    is_pro BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Categories Table
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    icon VARCHAR(100),
    parent_id UUID REFERENCES categories(id),
    order_index INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Ads Table
CREATE TABLE ads (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    category_id UUID REFERENCES categories(id),
    title VARCHAR(100) NOT NULL,
    description TEXT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    images TEXT[],
    city VARCHAR(100),
    zip_code VARCHAR(10),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    is_urgent BOOLEAN DEFAULT FALSE,
    status VARCHAR(20) DEFAULT 'published',
    views INTEGER DEFAULT 0,
    published_at TIMESTAMP,
    expires_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Conversations Table
CREATE TABLE conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ad_id UUID REFERENCES ads(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_message_at TIMESTAMP
);

-- Conversation Participants Table
CREATE TABLE conversation_participants (
    conversation_id UUID REFERENCES conversations(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    PRIMARY KEY (conversation_id, user_id)
);

-- Messages Table
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    conversation_id UUID REFERENCES conversations(id) ON DELETE CASCADE,
    sender_id UUID REFERENCES users(id) ON DELETE CASCADE,
    content TEXT NOT NULL,
    attachments TEXT[],
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Favorites Table
CREATE TABLE favorites (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    ad_id UUID REFERENCES ads(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(user_id, ad_id)
);

-- Indexes
CREATE INDEX idx_ads_user_id ON ads(user_id);
CREATE INDEX idx_ads_category_id ON ads(category_id);
CREATE INDEX idx_ads_status ON ads(status);
CREATE INDEX idx_ads_published_at ON ads(published_at);
CREATE INDEX idx_ads_location ON ads(city, zip_code);
CREATE INDEX idx_messages_conversation_id ON messages(conversation_id);
CREATE INDEX idx_messages_sender_id ON messages(sender_id);
```

#### Stratégie Redis

```
# Cache Keys Structure
user:{userId}                    # Cache profil utilisateur
ads:list:{filters_hash}          # Cache liste d'annonces
ad:{adId}                        # Cache détail annonce
search:{query_hash}              # Cache résultats recherche
session:{sessionId}              # Session utilisateur

# TTL Configuration
user: 1 hour
ads:list: 5 minutes
ad: 30 minutes
search: 10 minutes
session: 24 hours
```

### 4. API Design

#### REST API Endpoints

```
Authentication
POST   /api/auth/register              # Inscription
POST   /api/auth/login                 # Connexion
POST   /api/auth/logout                # Déconnexion
POST   /api/auth/refresh               # Rafraîchir token
POST   /api/auth/forgot-password       # Mot de passe oublié
POST   /api/auth/reset-password        # Réinitialiser MDP

Ads
GET    /api/ads                        # Liste annonces
GET    /api/ads/:id                    # Détail annonce
POST   /api/ads                        # Créer annonce
PUT    /api/ads/:id                    # Modifier annonce
DELETE /api/ads/:id                    # Supprimer annonce
GET    /api/ads/:id/similar            # Annonces similaires
POST   /api/ads/:id/views              # Incrémenter vues

Users
GET    /api/users/me                   # Mon profil
PUT    /api/users/me                   # Modifier profil
GET    /api/users/:id                  # Profil public
GET    /api/users/me/ads               # Mes annonces
GET    /api/users/me/favorites         # Mes favoris
POST   /api/users/me/favorites/:adId   # Ajouter favori
DELETE /api/users/me/favorites/:adId   # Retirer favori

Messages
GET    /api/messages/conversations     # Liste conversations
GET    /api/messages/:conversationId   # Messages conversation
POST   /api/messages                   # Envoyer message
PUT    /api/messages/:id/read          # Marquer lu

Categories
GET    /api/categories                 # Liste catégories
GET    /api/categories/:id             # Détail catégorie

Search
GET    /api/search                     # Recherche
GET    /api/search/autocomplete        # Autocomplétion
GET    /api/search/popular             # Recherches populaires
```

#### WebSocket Events

```javascript
// Client → Server
'message:send'         # Envoyer un message
'typing:start'         # Début de frappe
'typing:stop'          # Fin de frappe
'online'               # Utilisateur en ligne

// Server → Client
'message:received'     # Message reçu
'message:read'         # Message lu
'typing'               # Quelqu'un écrit
'notification'         # Nouvelle notification
'ad:updated'           # Annonce mise à jour
```

### 5. Sécurité

#### Authentification JWT

```typescript
// Token Structure
{
  header: {
    alg: "HS256",
    typ: "JWT"
  },
  payload: {
    userId: "uuid",
    email: "user@example.com",
    role: "user",
    iat: 1234567890,
    exp: 1234571490  // 1 hour
  },
  signature: "..."
}

// Refresh Token
{
  userId: "uuid",
  tokenId: "uuid",
  exp: 1234657890  // 7 days
}
```

#### Mesures de Sécurité

1. **Input Validation**: Joi / Yup schemas
2. **SQL Injection**: Parameterized queries (ORM)
3. **XSS Protection**: DOMPurify, CSP headers
4. **CSRF Protection**: CSRF tokens
5. **Rate Limiting**: Express-rate-limit
6. **Password Security**: bcrypt (cost factor: 12)
7. **HTTPS**: TLS 1.3 obligatoire
8. **Headers Security**: Helmet.js
9. **CORS**: Configuration restrictive
10. **File Upload**: Type validation, size limits

### 6. Performance et Scalabilité

#### Stratégies de Cache

```
┌─────────────┐
│   Browser   │ ← Cache client (Service Worker)
└─────────────┘
      ↓
┌─────────────┐
│     CDN     │ ← Cache statique (images, CSS, JS)
└─────────────┘
      ↓
┌─────────────┐
│  Redis      │ ← Cache applicatif (sessions, données)
└─────────────┘
      ↓
┌─────────────┐
│  Database   │ ← Données persistantes
└─────────────┘
```

#### Optimisations

1. **Database**:
   - Indexes sur colonnes fréquentes
   - Connection pooling
   - Query optimization
   - Partitioning pour grandes tables

2. **API**:
   - Response compression (gzip)
   - Pagination (cursor-based)
   - Field filtering
   - Batch requests

3. **Frontend**:
   - Code splitting
   - Lazy loading
   - Image optimization
   - Bundle minimization

4. **Infrastructure**:
   - Load balancing
   - Horizontal scaling
   - CDN pour assets
   - Auto-scaling

### 7. Monitoring et Logging

#### Logs Structure

```typescript
{
  timestamp: "2024-01-01T12:00:00Z",
  level: "info" | "warn" | "error",
  service: "api" | "worker" | "cron",
  message: "User logged in",
  userId: "uuid",
  requestId: "uuid",
  duration: 123,  // ms
  metadata: {}
}
```

#### Métriques à Surveiller

- **Performance**: Response time, throughput
- **Erreurs**: Error rate, 5xx errors
- **Business**: New ads, active users, messages sent
- **Infrastructure**: CPU, memory, disk usage
- **Database**: Query time, connections, cache hit rate

### 8. Déploiement

#### Architecture de Déploiement

```
┌──────────────────────────────────────────────┐
│              Load Balancer                    │
└──────────────────────────────────────────────┘
                    ↓
      ┌─────────────┴─────────────┐
      ↓                           ↓
┌──────────┐              ┌──────────┐
│  Web     │              │  Web     │
│  Server  │              │  Server  │
│  (Node)  │              │  (Node)  │
└──────────┘              └──────────┘
      ↓                           ↓
┌──────────────────────────────────────────────┐
│              Database Cluster                 │
│  ┌──────────┐        ┌──────────┐           │
│  │ Primary  │───────▶│ Replica  │           │
│  └──────────┘        └──────────┘           │
└──────────────────────────────────────────────┘
```

#### CI/CD Pipeline

```
Code Push → GitHub
    ↓
Build & Test → GitHub Actions
    ↓
Security Scan → CodeQL
    ↓
Build Docker Image
    ↓
Push to Registry
    ↓
Deploy to Staging
    ↓
Integration Tests
    ↓
Manual Approval
    ↓
Deploy to Production
    ↓
Health Check
```

## Conclusion

Cette architecture permet:
- **Scalabilité**: Peut gérer une croissance importante
- **Maintenabilité**: Code modulaire et bien organisé
- **Performance**: Optimisations à tous les niveaux
- **Sécurité**: Multiples couches de protection
- **Évolutivité**: Facile d'ajouter de nouvelles fonctionnalités
