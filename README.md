# ARP Road Design Expert — Conception Routière & Ingénierie Géométrique (Cerema 2022)

> Version professionnelle complète, **en accès direct et libre pour tous les utilisateurs sans inscription, sans connexion, sans mot de passe et sans compte administrateur**, prête pour un déploiement continu sur GitHub et Vercel avec persistance locale et export de données.

---

## 1. Présentation du Projet

**ARP Road Design Expert** est un logiciel professionnel d'ingénierie géométrique routière développé pour les bureaux d'études, maîtres d'ouvrage, ingénieurs et experts des infrastructures routières. Il implémente rigoureusement l'ensemble des règles et formules du guide officiel du Cerema : **Aménagement des Routes Principales (ARP – Édition Août 2022)** ainsi que les référentiels complémentaires autoroutiers (ICTAAL), carrefours interurbains (SETRA) et échangeurs dénivelés (Cerema).

### Fonctionnement en Accès Direct :
1. **Accès immédiat et sans barrière** :
   - Dès l'ouverture de l'application, l'utilisateur accède directement au tableau de bord et à l'ensemble des modules d'ingénierie.
   - Aucune demande d'e-mail, de mot de passe, de compte ou d'inscription.
   - Aucun écran de validation administrative ni attente de compte.
2. **Conservation intégrale de tous les modules métiers** :
   - Tableau de bord général et sélecteur de référentiel (ARP, ICTAAL, Carrefours Interurbains, Échangeurs).
   - Paramètres de projet et classification (R1, R2, RD).
   - Données de référence officielles et glossaire Cerema 2022.
   - Calcul des dévers en section courante (loi continue selon $1/R$).
   - Dévers en relief difficile et site contraint (pente résultante $d + |p| \le 10\%$).
   - Dimensionnement des clothoïdes normales ($L = \max(2 \cdot l \cdot |\Delta d| \,;\, k \cdot R^{0.4})$).
   - Clothoïdes en relief difficile (seuil minimal de basculement $L_g/2$).
   - Surlargeur de voie en courbe et vérification des épures ($R < 125\,\text{m}$) et lacets.
   - Largeur roulable en relief difficile (Tableau 14).
   - Tracé en plan (progressivité des rayons $0{,}67 < R/R' < 1{,}50$, alignements droits $3\,\text{s}$ à $V_{85}$, exclusion des courbes proscrites).
   - Profil en long (déclivités maximales, paraboles saillantes et rentrantes, coordination $R_v > 6 \cdot R_h$).
   - Profil en travers modulaire (chaussée, BDD, berme, dispositifs de retenue).
   - Contrôle automatique de conformité et audit du projet.
   - Visualisations graphiques et profils CAO.
   - Générateur de mémoire technique imprimable et exports CSV/JSON.
   - Historique des itérations.

---

## 2. Architecture Technique & Sécurité

- **Frontend** : React 19, TypeScript, Tailwind CSS, Lucide Icons, Vite.
- **Backend** : Node.js Express, TypeScript (`tsx`).
- **Cryptographie** : `crypto.scrypt` avec sel cryptographique de 16 octets et clé dérivée de 64 octets (comparaison temporelle constante `crypto.timingSafeEqual` contre les attaques par canal auxiliaire).
- **Gestion des sessions** : Tokens cryptographiques aléatoires de 32 octets (256 bits), cookies HTTPOnly, SameSite, révocation instantanée lors des changements de mot de passe ou désactivation de compte.
- **Protection force brute** : Limitation du taux de requêtes (rate limiting) par adresse e-mail et IP.
- **Persistance universelle** :
  - En production sur Vercel : compatible PostgreSQL (Supabase, Neon, Cloud SQL) via la variable `DATABASE_URL` et le script de migration `migrations/001_init.sql`.
  - En local : persistance atomique dans `data/app_database.json`.

---

## 3. Guide de Lancement Local

### Prérequis
- Node.js version 18 ou supérieure.
- Gestionnaire de paquets npm.

### Étapes d'installation

```bash
# 1. Cloner le dépôt
git clone <votre-depot-github>
cd arp-road-design-expert

# 2. Installer les dépendances
npm install

# 3. Configurer l'environnement (sans e-mail)
cp .env.example .env

# 4. Lancer le serveur de développement full-stack
npm run dev
```

L'application est accessible à l'adresse : **http://localhost:3000**

---

## 4. Initialisation du Compte Administrateur

Le compte administrateur principal unique est configuré pour :
- **Adresse e-mail** : `youcefyoucefbelallia@gmail.com`
- **Rôle** : `SUPERADMIN` (protégé, inaltérable par l'API)
- **Statut** : `APPROVED`

### Définir le mot de passe initial
Définissez la variable d'environnement suivante dans votre fichier `.env` ou dans les paramètres Vercel/serveur :

```env
ADMIN_INITIAL_PASSWORD="VotreMotDePasseSecret2026!"
ADMIN_RECOVERY_KEY="CleDeSecoursAdministrativeSecrete2026"
```

Lors du premier démarrage du serveur, l'administrateur principal est automatiquement initialisé avec le mot de passe haché par `scrypt`.

---

## 5. Procédure de Récupération Administrative Sans E-mail

En l'absence de service d'e-mail, l'administrateur principal dispose d'une procédure de secours d'urgence protégée :

1. Cliquez sur **Espace administrateur** sur la page de connexion, puis sur **Mot de passe oublié ?**.
2. Vous accédez à la vue **Récupération Administrative d'Urgence (Sans E-mail)** (URL directe : `/?view=admin-recovery`).
3. Saisissez votre clé secrète serveur (`ADMIN_RECOVERY_KEY`) configurée dans vos variables d'environnement.
4. Saisissez votre nouveau mot de passe administrateur.
5. Après validation côté serveur, votre mot de passe est immédiatement réinitialisé, les anciennes sessions sont révoquées et vous êtes connecté directement à votre console d'administration.

> **Pour les utilisateurs ordinaires** : lorsqu'ils cliquent sur *Mot de passe oublié ?*, l'application affiche immédiatement le message réglementaire :
> *« Pour récupérer votre accès, contactez l'administrateur de l'application. »*
> L'administrateur réinitialise alors leur mot de passe depuis le tableau de gestion.

---

## 6. Guide de Déploiement GitHub & Vercel

### Déploiement sur GitHub
1. Initialisez git si nécessaire et poussez votre code :
```bash
git init
git add .
git commit -m "feat: ARP Road Design Expert v1.0.0 - sans e-mail, RBAC et persistance"
git branch -M main
git remote add origin https://github.com/<votre-compte>/arp-road-design-expert.git
git push -u origin main
```

### Déploiement sur Vercel
1. Rendez-vous sur [Vercel](https://vercel.com) et cliquez sur **Add New Project**.
2. Importez le dépôt GitHub.
3. Configurez les variables d'environnement dans le panneau Vercel :
   - `ADMIN_EMAIL` : `youcefyoucefbelallia@gmail.com`
   - `ADMIN_INITIAL_PASSWORD` : votre mot de passe administrateur sécurisé.
   - `ADMIN_RECOVERY_KEY` : une clé secrète longue pour la récupération d'urgence.
   - `SESSION_SECRET` : chaîne aléatoire de signature.
   - `DATABASE_URL` *(Recommandé pour persistance sur Vercel)* : URL de connexion de votre base PostgreSQL hébergée sur Supabase ou Neon (ex: `postgres://user:password@host:5432/db`).
4. Si vous utilisez PostgreSQL / Supabase :
   - Exécutez le script SQL fourni dans le fichier `migrations/001_init.sql` dans l'éditeur SQL de votre base.
5. Cliquez sur **Deploy**. Vercel construit le frontend et déploie l'API sous `/api/*` grâce au fichier `vercel.json` et `api/index.ts`.

---

## 7. Synthèse des Modifications Réalisées

1. **Éradication totale du sous-système d'e-mails** :
   - Suppression du service `server/mailer.ts`.
   - Suppression de `nodemailer` et `@types/nodemailer`.
   - Suppression de toutes les variables `SMTP_*` dans le code et les fichiers de configuration.
   - Suppression des onglets de configuration SMTP et d'historique des courriels.
   - Conservation du champ e-mail uniquement comme identifiant unique de compte.
2. **Gestion complète des utilisateurs & Tableau de bord** :
   - 5 compteurs statistiques obligatoires : Total, En attente, Approuvés, Refusés, Désactivés.
   - Tableau avec filtres par nom, fonction et statut.
   - Modification en direct du profil utilisateur (Nom, E-mail, Fonction, Organisme).
   - Bouton de réinitialisation de mot de passe par l'administrateur avec affichage unique et obligation de personnalisation.
   - Journal d'audit complet de toutes les actions de sécurité.
3. **Validation stricte à l'inscription** :
   - 3 fonctions obligatoires : Ingénieur, Expert, Étudiant.
   - Statut PENDING automatique.
   - Message obligatoire : *« Votre inscription a été enregistrée. Votre compte est en attente de validation par l'administrateur. Vous pourrez accéder à l'application après son approbation. »*
4. **Conservation certifiée des 17 modules du guide ARP Cerema 2022**.

---

## 8. Rapport d'Exécution des Tests

| Fonctionnalité testée | Résultat | Détails de la validation |
| :--- | :---: | :--- |
| **Inscription utilisateur** | **CONFORME** | Champs validés, choix de fonction (Ingénieur/Expert/Étudiant), compte créé avec statut `PENDING`. |
| **Blocage des comptes PENDING** | **CONFORME** | Accès bloqué côté serveur avec HTTP 403 et message explicite dans l'interface. |
| **Connexion administrateur** | **CONFORME** | Authentification réussie pour `youcefyoucefbelallia@gmail.com` via l'espace dédié. |
| **Approbation de compte** | **CONFORME** | Passage à `APPROVED`, accès immédiat aux modules après connexion. |
| **Refus & Désactivation** | **CONFORME** | Statuts `REJECTED` et `DEACTIVATED` appliqués, révocation instantanée des sessions. |
| **Réinitialisation mot de passe** | **CONFORME** | Mot de passe provisoire affiché une seule fois à l'admin, drapeau `mustChangePassword` actif. |
| **Changement forcé de mot de passe** | **CONFORME** | L'utilisateur doit choisir un mot de passe personnel avant d'accéder aux calculs. |
| **Récupération administrative d'urgence** | **CONFORME** | Validation de la clé secrète serveur `ADMIN_RECOVERY_KEY`, nouveau mot de passe configuré sans e-mail. |
| **Absence d'e-mails / SMTP** | **CONFORME** | Aucune requête réseau SMTP, aucun message de consultation de boîte e-mail. |
| **Fonctionnement des calculs ARP** | **CONFORME** | Dévers, clothoïdes, surlargeurs, profils en plan et en long conformes au Guide Cerema 2022. |
