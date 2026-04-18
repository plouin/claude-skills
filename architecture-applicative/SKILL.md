---
name: architecture-applicative
description: Utilise ce skill dès que l'utilisateur veut documenter, décrire, rédiger ou produire la fiche d'architecture d'une application (une à la fois). Déclencheurs typiques : "fiche d'archi", "architecture applicative", "doc d'archi", "décris l'archi de tel service", "produis la fiche technique de telle appli", "documentation d'architecture", ou toute demande de description structurée d'une application couvrant ses couches infra/technique ET logicielle (domaine, FQDN, hébergement, conteneurs, backend, frontend, CI/CD, registry, etc.). Utilise aussi si l'utilisateur fournit en vrac des infos sur une appli (IP, version, git, etc.) et veut les structurer. Sortie : un document Markdown structuré selon le template ci-dessous. Ne pas confondre avec la cartographie SI (plusieurs applis) — ce skill couvre UNE application à la fois. Ne pas utiliser pour maquettes UX, archi de données transverses, ou architecture d'entreprise globale.
---

# Fiche d'architecture applicative

## Objectif

Produire un document Markdown standardisé qui décrit **une seule application** sous ses deux faces : **architecture technique / infrastructure** et **architecture logicielle**. Le document doit être directement utilisable comme référence dans une base de connaissances, un wiki ou un dossier d'exploitation.

## Mode de fonctionnement

Extraction **passive** : remplir les champs à partir des informations fournies par l'utilisateur (conversation, fichiers uploadés, contexte). Pour tout champ sans information disponible :
- indiquer `_à préciser_` en italique
- ne **pas** inventer de valeur
- ne **pas** poser de question à l'utilisateur (sauf si celui-ci demande explicitement un mode interactif)

L'objectif est que la fiche soit honnête sur les zones d'ombre : un champ vide bien signalé est plus utile qu'une valeur inventée.

## Structure de sortie

Utiliser exactement cette structure, dans cet ordre, avec ces titres. Conserver les sections même si elles sont partiellement vides (la présence d'un `_à préciser_` documente une dette de documentation à combler).

```markdown
# Fiche d'architecture — <Nom de l'application>

> **Résumé** : <1-2 phrases décrivant ce que fait l'appli et son contexte métier>
>
> **Statut** : <prod | pré-prod | dev | POC>  •  **Criticité** : <faible | moyenne | forte>  •  **Dernière mise à jour** : <YYYY-MM-DD>

---

## 1. Architecture technique / infrastructure

### 1.1 Domaine
- **FQDN principal** :
- **Propriétaire du domaine** :
- **Registrar** :
- **Émetteur du certificat TLS** :
- **Échéance certificat** :

### 1.2 Front (exposition)
- **FQDN exposé** :
- **IP publique** :
- **Ports ouverts** :
- **Règles firewall** (sources autorisées, restrictions) :
- **Service web** (reverse proxy / serveur) — logiciel et version :

### 1.3 SMTP (envoi de mails)
- **Fournisseur** (Brevo, Mailjet, SES, SMTP interne…) :
- **Compte / sender** :
- **Domaine d'envoi + SPF/DKIM/DMARC** :

### 1.4 Service de données
- **Type** (PostgreSQL, MySQL, MongoDB, Redis, S3…) :
- **Logiciel et version** :
- **Hébergement** (managé / self-hosted, localisation) :
- **Stratégie de sauvegarde** (fréquence, rétention, lieu, tests de restauration) :

### 1.5 Exécution
Préciser VM **et/ou** container selon le cas.

**VM :**
- **Hyperviseur** (Proxmox, VMware, cloud…) :
- **OS et version** :
- **Ressources** (vCPU / RAM / disque) :

**Container :**
- **Orchestrateur** (Kubernetes, Docker Swarm, Nomad…) et version :
- **Namespace / cluster** :
- **Ressources** (requests/limits) :

### 1.6 Infrastructure as Code
- **Outil** (Terraform, Pulumi, Ansible…) :
- **Repository** (ex : `gitlab.acta-ds.fr/...`) :
- **State / backend** :

### 1.7 Batch / tâches planifiées
- **Ordonnanceur** (cron, Kubernetes CronJob, Airflow, n8n…) :
- **Liste des jobs** (nom, fréquence, fonction) :

---

## 2. Architecture logicielle

### 2.1 Frontend
- **Langage** :
- **Framework / logiciel** :
- **Version** :
- **Build / packaging** :

### 2.2 API / Backend
- **Langage** :
- **Framework / logiciel** :
- **Version** :
- **Authentification** (OAuth, JWT, session…) :
- **Dépendances externes notables** :

### 2.3 Gestion de flux / intégration
- **Outil** (n8n, Zapier, Make, Airflow, scripts custom…) :
- **Workflows principaux** :

### 2.4 Repository Git
- **URL** (ex : `gitlab.acta-ds.fr/...`) :
- **Branche de prod** :
- **Conventions** (trunk-based, GitFlow…) :

### 2.5 Registre d'images
- **URL** (ex : `registry.acta-ds.fr/...`) :
- **Images publiées** :

### 2.6 Chaîne d'intégration / déploiement continu
- **CI** (GitLab CI, GitHub Actions…) :
- **CD** (ex : `argocd.ads.kiowy.io`, Flux, déploiement manuel) :
- **Environnements** (dev / staging / prod) :
- **Stratégie de release** (blue/green, canary, rolling…) :

---

## 3. Points d'attention

Lister ici tout ce qui mérite vigilance : dette technique, fins de support annoncées, dépendances critiques, SPOF connus, audits à prévoir.

- _à préciser_

## 4. Contacts

- **Responsable produit** :
- **Responsable technique** :
- **Hébergeur / infogérance** :
- **Astreinte** :
```

## Règles de remplissage

**Versions** : toujours préciser la version majeure **et** mineure si disponible (ex : `PostgreSQL 15.4`, pas juste `PostgreSQL`). Si on ne connaît que la majeure, l'indiquer tel quel (`PostgreSQL 15.x`).

**URLs et FQDN** : les citer sans formatage de lien Markdown sauf si utile (pour faciliter la copie).

**Champs booléens ou de statut** : privilégier des valeurs normalisées (`oui`/`non`, `activé`/`désactivé`, `prod`/`pré-prod`/`dev`) pour permettre d'agréger plusieurs fiches plus tard.

**Exemples implicites** : les exemples dans le template (`gitlab.acta-ds.fr`, `registry.acta-ds.fr`, `argocd.ads.kiowy.io`, `Brevo`, etc.) sont des hints destinés au lecteur ; ne pas les écrire dans la fiche finale s'ils ne correspondent pas à la réalité de l'appli documentée.

**Dette de documentation** : à la fin, compter le nombre de `_à préciser_` et le signaler en une ligne à l'utilisateur après avoir livré la fiche (ex : _"12 champs à compléter — principalement sur la section SMTP et batch."_). Cela aide à prioriser les entretiens complémentaires.

## Livraison

Sauvegarder la fiche dans `/sessions/keen-funny-cerf/mnt/outputs/` avec un nom de la forme `archi-<slug-appli>.md`, puis fournir un lien `computer://` à l'utilisateur. Réponse finale courte et factuelle, conformément aux préférences de Guillaume (synthétique, minimaliste).
