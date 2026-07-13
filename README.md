# Brame Développement · Dashboard Pricing V9

Dashboard de pilotage pricing CAP'EMBAL & LEGAA — données Odoo.

## Fichiers

- `index.html` — portail de login (page d'accueil)
- `dashboard.html` — dashboard complet (protégé)
- `README.md` — ce fichier

## Publication sur GitHub Pages

### Étape 1 — Créer un repo privé
```
1. github.com → New repository
2. Name : "brame-pricing" (ou autre)
3. Cocher "Private"
4. Ne pas ajouter README (déjà présent)
5. Create repository
```

### Étape 2 — Uploader les fichiers
- Sur la page du repo → "uploading an existing file"
- Glisser-déposer `index.html`, `dashboard.html`, `README.md`
- Commit changes

### Étape 3 — Activer GitHub Pages
```
1. Repo → Settings → Pages
2. Source : Deploy from a branch
3. Branch : main / (root)
4. Save
```

L'URL sera de la forme : `https://<ton-user>.github.io/brame-pricing/`

L'URL est **techniquement accessible sans être membre du repo** (GitHub Pages sert toujours en public, même depuis un repo privé sauf plan Enterprise). D'où l'importance du portail login côté client — voir ci-dessous les limites.

## Gestion des utilisateurs

Éditer `index.html`, section `VALID_HASHES`, pour ajouter/retirer des couples user:password.

### Générer un nouveau hash

En terminal (macOS/Linux) :
```bash
echo -n "monuser:monmotdepasse" | sha256sum
```

En Python :
```python
import hashlib
print(hashlib.sha256(b"monuser:monmotdepasse").hexdigest())
```

En ligne de commande PowerShell (Windows) :
```powershell
$str = "monuser:monmotdepasse"
[System.BitConverter]::ToString([System.Security.Cryptography.SHA256]::Create().ComputeHash([Text.Encoding]::UTF8.GetBytes($str))) -replace '-',''
```

Puis coller le hash dans le tableau `VALID_HASHES` :
```javascript
const VALID_HASHES = [
    "c26c983754f59510cb9bcf36858940ced84210211bd844b98a796a494f58dd6d", // alexandre
    "aa1bb2cc3dd4...",  // nouveau collaborateur
];
```

## Comptes par défaut

| User | Password |
|---|---|
| alexandre | brame2026 |

**À changer avant publication.**

## ⚠️ IMPORTANT — Limites de sécurité

Ce portail login est une **barrière visuelle**, pas une vraie protection des données :

1. **Le fichier `dashboard.html` contient toutes les données commerciales** (CA par sous-cat, clients, marges) embarquées dans le JavaScript.
2. Une personne technique peut :
   - Aller directement à l'URL `dashboard.html` (le guard JS redirige, mais le fichier reste téléchargeable)
   - Utiliser Chrome DevTools → Sources → voir le contenu complet
   - Contourner le portail en supprimant la logique de check

**Le portail login empêche les utilisateurs non-techniques d'accéder par curiosité, mais ne résiste pas à quelqu'un de motivé.**

### Alternative : vraie protection avec Cloudflare Access (recommandé)

Pour une vraie sécurité (auth SSO/email, logs d'accès, révocation) :

1. Créer un compte Cloudflare (gratuit)
2. Cloudflare Pages : connecter ton repo GitHub → déploiement auto
3. Cloudflare Access : gratuit jusqu'à 50 users
   - Créer une "application" pointant vers le dashboard
   - Politique : "Emails within..." avec liste blanche d'emails
   - Les utilisateurs reçoivent un code par email à chaque connexion

Coût : 0 €. Setup : ~15 minutes. Vraie sécurité entreprise-grade.

### Alternative : héberger en interne

Si le dashboard doit rester à Brame Développement uniquement, l'idéal est de l'héberger sur un serveur interne avec authentification HTTP Basic (Apache/Nginx) ou avec Cloudflare Zero Trust.

## Mise à jour du dashboard

Quand tu génères une nouvelle version du dashboard :

1. Remplace `dashboard.html` dans le repo GitHub
2. Les paramètres saisis par les utilisateurs sont dans **leur navigateur (localStorage)** — ils ne sont pas affectés par la mise à jour du code
3. Ils peuvent exporter/importer leur JSON de paramètres si besoin (bouton dans l'onglet Paramètres)

## Session

- Durée de session : 8 heures
- Stockée dans `sessionStorage` (perdue si onglet fermé)
- Bouton "↷ Déconnexion" en haut à droite du dashboard
