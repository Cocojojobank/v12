# JK CAPITAL — Node.js + PostgreSQL (sans Prisma)

Cette version démarre directement avec Node.js et Express. Prisma, Prisma Composer et Prisma Cloud ont été retirés.

## Technologies
- Node.js + Express
- PostgreSQL avec le pilote `pg` (SQL direct)
- EJS pour les vues
- Sessions stockées dans PostgreSQL (`connect-pg-simple`)
- Déploiement Render

## Déploiement sur Render

1. Décompresse l'archive et téléverse **tout son contenu** à la racine du dépôt GitHub.
2. Dans Render, crée un **Web Service** et connecte le dépôt GitHub.
3. Render détecte `render.yaml` (Blueprint) ou configure manuellement :
   - Runtime : Node
   - Build command : `npm install`
   - Start command : `npm start`
   - Health check path : `/health`
4. Ajoute les variables d'environnement :
   - `DATABASE_URL` : URL de connexion PostgreSQL complète.
   - `SESSION_SECRET` : secret aléatoire d'au moins 32 caractères.
   - `ADMIN_EMAIL` : e-mail du compte administrateur initial.
   - `ADMIN_PASSWORD` : mot de passe administrateur fort.
   - `PUBLIC_REGISTRATION=false` par défaut.
   - `DEMO_MODE=false` par défaut.
   - `CURRENCY=EUR` par défaut.

`render.yaml` génère automatiquement `SESSION_SECRET`; les trois autres secrets/paramètres requis doivent être fournis dans Render.

## PostgreSQL et conservation des données

L'application utilise directement PostgreSQL via `pg` et crée ses tables avec `CREATE TABLE IF NOT EXISTS`. Cette initialisation ne supprime pas les tables existantes.

Pour conserver les données déjà présentes, utilise une URL PostgreSQL qui pointe vers **la base existante**, à condition que cette base accepte les connexions depuis Render. Si l'ancienne base fournie par Prisma Cloud n'est pas accessible depuis Render, il faudra exporter puis importer les données dans une base PostgreSQL accessible à Render. Ne remplace pas `DATABASE_URL` avant d'avoir sauvegardé les données importantes.

## Vérification

Après déploiement, ouvre `https://TON-SERVICE.onrender.com/health`. Une réponse attendue est :

```json
{"status":"ok","mode":"test-configured","database":"connected"}
```

## Limites fonctionnelles importantes

Le projet conserve les parcours de démonstration : inscriptions (si activées), connexion, demandes fictives de dépôt/retrait, administration et journal de solde. Les dépôts et retraits sont simulés : aucun paiement ni virement réel n'est exécuté et aucun rendement n'est garanti. Ne l'utilise pas pour recevoir ou gérer de vrais fonds sans audit de sécurité, conformité et intégration de paiement appropriée.

## Exécution locale

```bash
cp .env.example .env
npm install
npm start
```

Renseigne une vraie URL PostgreSQL dans `.env` avant de démarrer.
