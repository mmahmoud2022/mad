# Guide de Contribution

Merci de votre intérêt pour contribuer au projet MAD ! Ce document décrit comment contribuer efficacement au projet.

## Table des Matières

- [Code de Conduite](#code-de-conduite)
- [Comment Contribuer](#comment-contribuer)
- [Standards de Code](#standards-de-code)
- [Process de Pull Request](#process-de-pull-request)
- [Signalement de Bugs](#signalement-de-bugs)
- [Suggestions de Fonctionnalités](#suggestions-de-fonctionnalités)

## Code de Conduite

En participant à ce projet, vous acceptez de respecter notre code de conduite. Soyez respectueux et professionnel dans toutes vos interactions.

## Comment Contribuer

### 1. Fork le Repository

```bash
# Forker le projet sur GitHub, puis cloner
git clone https://github.com/votre-username/mad.git
cd mad
```

### 2. Créer une Branche

```bash
# Créer une branche pour votre fonctionnalité ou correction
git checkout -b feature/ma-nouvelle-fonctionnalite
# ou
git checkout -b fix/correction-bug
```

### 3. Faire vos Modifications

- Suivez les standards de code du projet
- Ajoutez des tests si applicable
- Mettez à jour la documentation si nécessaire
- Committez régulièrement avec des messages clairs

### 4. Tester vos Modifications

```bash
# Backend
cd backend
npm test
npm run lint

# Frontend
cd frontend
npm test
npm run lint
```

### 5. Soumettre une Pull Request

- Poussez votre branche sur GitHub
- Ouvrez une Pull Request
- Décrivez clairement vos changements
- Référencez les issues concernées

## Standards de Code

### JavaScript/TypeScript

- Utiliser ESLint et Prettier
- Suivre les conventions Airbnb
- Utiliser TypeScript pour le typage
- Nommer les variables en camelCase
- Nommer les classes en PascalCase
- Utiliser des noms descriptifs

**Exemple:**
```typescript
// ✅ Bon
const getUserById = async (userId: string): Promise<User> => {
  const user = await userRepository.findOne(userId);
  return user;
};

// ❌ Mauvais
const get = async (id: string) => {
  const u = await repo.find(id);
  return u;
};
```

### Commits

Format des messages de commit:

```
type(scope): description courte

Description plus longue si nécessaire

Fixes #123
```

**Types:**
- `feat`: Nouvelle fonctionnalité
- `fix`: Correction de bug
- `docs`: Documentation
- `style`: Formatage, style
- `refactor`: Refactorisation
- `test`: Ajout de tests
- `chore`: Maintenance

**Exemples:**
```
feat(ads): add image upload functionality
fix(auth): resolve token expiration issue
docs(api): update authentication endpoints
```

### Tests

- Écrire des tests unitaires pour la logique métier
- Écrire des tests d'intégration pour les API
- Viser une couverture de code > 80%
- Utiliser des noms de tests descriptifs

**Exemple:**
```typescript
describe('AdService', () => {
  describe('createAd', () => {
    it('should create an ad with valid data', async () => {
      // Arrange
      const adData = { title: 'Test', price: 100 };
      
      // Act
      const result = await adService.createAd(adData);
      
      // Assert
      expect(result).toBeDefined();
      expect(result.title).toBe('Test');
    });

    it('should throw error with invalid price', async () => {
      // Arrange
      const adData = { title: 'Test', price: -100 };
      
      // Act & Assert
      await expect(adService.createAd(adData))
        .rejects.toThrow('Price must be positive');
    });
  });
});
```

### Documentation

- Documenter les fonctions publiques
- Utiliser JSDoc pour TypeScript
- Mettre à jour le README si nécessaire
- Documenter les endpoints API

**Exemple:**
```typescript
/**
 * Creates a new advertisement
 * @param adData - The advertisement data
 * @param userId - The user ID creating the ad
 * @returns The created advertisement
 * @throws {ValidationError} If the ad data is invalid
 */
async createAd(adData: CreateAdDto, userId: string): Promise<Ad> {
  // Implementation
}
```

## Process de Pull Request

### Checklist avant Soumission

- [ ] Code testé et fonctionnel
- [ ] Tests passent (npm test)
- [ ] Linting passé (npm run lint)
- [ ] Documentation mise à jour
- [ ] Commits bien formatés
- [ ] Branche à jour avec main

### Review Process

1. **Automated Checks**: CI/CD vérifie automatiquement
2. **Code Review**: Au moins un reviewer approuve
3. **Testing**: Tests manuels si nécessaire
4. **Merge**: Après approbation

### Temps de Review

- Bugs critiques: 24h
- Fonctionnalités: 2-3 jours
- Documentation: 1-2 jours

## Signalement de Bugs

### Template de Bug Report

```markdown
**Description du Bug**
Description claire et concise du bug.

**Pour Reproduire**
1. Aller à '...'
2. Cliquer sur '...'
3. Défiler jusqu'à '...'
4. Voir l'erreur

**Comportement Attendu**
Ce qui devrait se passer.

**Captures d'Écran**
Si applicable, ajouter des captures.

**Environnement:**
 - OS: [ex: Windows 10]
 - Navigateur: [ex: Chrome 96]
 - Version: [ex: 1.2.3]

**Contexte Additionnel**
Toute autre information pertinente.
```

## Suggestions de Fonctionnalités

### Template de Feature Request

```markdown
**La fonctionnalité est-elle liée à un problème?**
Description claire du problème.

**Solution Proposée**
Description de la solution souhaitée.

**Alternatives Considérées**
Autres solutions envisagées.

**Contexte Additionnel**
Informations supplémentaires.
```

## Structure des Branches

```
main              # Branche de production
├── develop       # Branche de développement
├── feature/*     # Nouvelles fonctionnalités
├── fix/*         # Corrections de bugs
├── hotfix/*      # Corrections urgentes
└── release/*     # Préparation de releases
```

## Labels GitHub

- `bug`: Bug à corriger
- `enhancement`: Nouvelle fonctionnalité
- `documentation`: Documentation
- `good first issue`: Bon pour débutants
- `help wanted`: Besoin d'aide
- `priority: high`: Haute priorité
- `priority: low`: Basse priorité
- `wontfix`: Ne sera pas corrigé

## Communication

- **Issues**: Pour bugs et fonctionnalités
- **Discussions**: Pour questions générales
- **Pull Requests**: Pour revue de code
- **Email**: pour questions privées

## Reconnaissance

Les contributeurs sont reconnus dans:
- README.md (section Contributors)
- CHANGELOG.md (avec chaque release)
- GitHub Contributors page

## Ressources

- [Documentation](./docs/)
- [API Reference](./docs/api.md)
- [Architecture](./docs/architecture.md)
- [Components](./docs/components.md)

## Questions?

N'hésitez pas à:
- Ouvrir une issue
- Rejoindre les discussions
- Contacter les mainteneurs

Merci de contribuer à MAD ! 🎉
