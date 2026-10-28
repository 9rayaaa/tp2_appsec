# JWT Demo API

> API REST d’authentification construite avec Spring Boot et Spring Security. Les utilisateurs peuvent créer un compte, se connecter et accéder aux routes protégées avec un jeton JWT.

## Sommaire

- [Fonctionnalités](#fonctionnalités)
- [Technologies](#technologies)
- [Démarrage](#démarrage)
- [Configuration](#configuration)
- [Routes de l’API](#routes-de-lapi)
- [Exemples d’utilisation](#exemples-dutilisation)
- [Structure du projet](#structure-du-projet)
- [Sécurité](#sécurité)
- [Auteur](#auteur)

## Fonctionnalités

- Inscription avec mot de passe haché par BCrypt
- Authentification et génération de jetons JWT signés
- Vérification des jetons à chaque requête protégée
- Stockage des utilisateurs dans une base H2 en mémoire
- Sessions Spring Security sans état (*stateless*)

## Technologies

| Outil | Version / usage |
|---|---|
| Java | 21 |
| Spring Boot | 3.4.13 |
| Spring Security | Authentification et autorisation |
| Spring Data JPA | Persistance des utilisateurs |
| H2 | Base de données en mémoire |
| JJWT | 0.13.0, création et validation des jetons |
| Maven | Compilation et lancement |

## Démarrage

Clonez le dépôt et lancez l’application :

```bash
git clone https://github.com/K4YR0/TP2_AppSec.git
cd TP2_AppSec
./mvnw spring-boot:run
```

L’API démarre à l’adresse `http://localhost:8080`.

## Configuration

Les paramètres se trouvent dans `src/main/resources/application.properties`. La configuration actuelle utilise H2 en mémoire et expose la console H2 sur `/h2-console`.

```properties
spring.datasource.url=jdbc:h2:mem:securitydb
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

jwt.secret=<clé encodée en Base64>
jwt.expiration=3600000
```

La durée du jeton est exprimée en millisecondes ; `3600000` correspond à une heure. La clé d’exemple du dépôt est réservée au développement. Utilisez un secret externe et privé pour tout déploiement.

## Routes de l’API

| Méthode | Route | Accès | Description |
|---|---|---|---|
| `POST` | `/auth/register` | Public | Crée un compte utilisateur |
| `POST` | `/auth/login` | Public | Vérifie les identifiants et retourne un JWT |
| `GET` | `/api/public` | Public | Retourne une réponse de démonstration publique |
| `GET` | `/api/hello` | Protégé | Retourne un message de bienvenue |
| `GET` | `/api/profile` | Protégé | Retourne l’identité de l’utilisateur connecté |
| `GET` | `/api/private` | Protégé | Retourne une réponse de démonstration protégée |

Les routes protégées attendent un en-tête `Authorization: Bearer <token>`.

## Exemples d’utilisation

### 1. Créer un compte

```bash
curl -X POST http://localhost:8080/auth/register \
  -H 'Content-Type: application/json' \
  -d '{"email":"a@a.com","password":"1234"}'
```

![Réponse à l’inscription](docs/images/01-register.png)

### 2. Se connecter

```bash
curl -X POST http://localhost:8080/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email":"a@a.com","password":"1234"}'
```

La réponse contient le jeton à transmettre aux routes protégées.

![Réponse à la connexion](docs/images/02-login.png)

### 3. Appeler une route protégée sans jeton

```bash
curl -i http://localhost:8080/api/hello
```

![Réponse sans jeton](docs/images/03-sans-token.png)

### 4. Appeler les routes protégées avec un jeton

```bash
TOKEN='<coller-le-jeton-reçu-à-la-connexion>'
curl http://localhost:8080/api/hello -H "Authorization: Bearer $TOKEN"
curl http://localhost:8080/api/profile -H "Authorization: Bearer $TOKEN"
```

![Réponse avec jeton](docs/images/04-avec-token.png)

## Structure du projet

```text
src/main/java/com/test/seance2/
├── config/       Configuration Spring Security
├── controller/   Contrôleurs d’authentification et de démonstration
├── dto/          Objets d’entrée et de réponse
├── entity/       Entité utilisateur
├── repository/   Accès aux utilisateurs
├── security/     Filtre et service JWT
└── service/      Chargement des utilisateurs
```

## Sécurité

- Gardez le secret JWT hors du dépôt et fournissez-le par variable d’environnement ou gestionnaire de secrets.
- Désactivez la console H2 et les journaux SQL en production.
- Ajoutez une validation des entrées et une politique de mot de passe adaptée.
- Pour une application en production, prévoyez la gestion du renouvellement et de la révocation des jetons.

## Auteur

**machnaoui abdellatif** — Cycle ingénieur en cybersécurité, ENSET Mohammedia
