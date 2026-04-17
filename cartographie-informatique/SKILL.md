---
name: cartographie-informatique
description: "Utilise ce skill dès que l'utilisateur veut cartographier un système d'information : cartographie d'infrastructure (réseau, serveurs, datacenters, cloud), cartographie applicative (applications, modules, APIs, flux inter-applicatifs), ou cartographie des données (bases, flux, lineage, référentiels). Déclencheurs : 'cartographie SI', 'schéma d'architecture', 'urbanisation', 'topologie réseau', 'diagramme d'architecture', 'flux applicatifs', 'data lineage', 'vue applicative', 'vue infrastructure', 'ArchiMate', 'TOGAF', 'C4 model'. Utilise aussi si l'utilisateur mentionne un inventaire d'applications/serveurs/bases à représenter visuellement, même sans prononcer le mot 'cartographie'. Sorties : diagrammes Mermaid et/ou PlantUML/ArchiMate, inventaire tabulaire, document de synthèse. Ne pas utiliser pour maquettes UX ou cartographie géographique."
---

# Cartographie informatique (SI)

## Objectif

Produire des cartographies de systèmes d'information structurées, versionnables (diagramme-as-code) et conformes aux bonnes pratiques d'architecture d'entreprise (TOGAF / ArchiMate, inspirations C4 et ANSSI).

## Démarche en 4 temps

Ne pas sauter d'étape. L'ordre compte : sans cadrage et sans inventaire, le diagramme reflète des suppositions, pas la réalité.

1. **Cadrer** — identifier le périmètre, la/les vue(s) demandée(s), l'audience (DSI, métier, sécurité, ops), et le niveau de détail attendu.
2. **Inventorier** — collecter les objets (applications, serveurs, flux, bases…) et leurs relations dans un tableau structuré avant de dessiner quoi que ce soit.
3. **Modéliser** — traduire l'inventaire en diagramme (Mermaid ou PlantUML/ArchiMate selon l'usage).
4. **Restituer** — livrer les fichiers source (.mmd, .puml) + un document de synthèse qui explique la lecture des schémas.

## Étape 1 — Cadrer

Avant de produire quoi que ce soit, clarifier avec l'utilisateur (via questions ou inférence à partir du contexte) :

- **Vue(s) demandée(s)** parmi les trois couches ArchiMate concernées :
  - *Infrastructure* (Technology Layer) : nœuds, device, network, artifacts déployés
  - *Applicative* (Application Layer) : application components, services, interfaces, flux
  - *Données* (Business/Application data) : data objects, data flows, stockages
- **Granularité** : vue macro (contexte, ~10 objets) ou vue détaillée (dizaines/centaines d'objets)
- **Audience** : conditionne la densité, le vocabulaire, l'usage de couleurs/légendes
- **Périmètre temporel** : cible, existant (AS-IS), trajectoire (TO-BE)
- **Données disponibles** : fichier Excel, CMDB, description libre, rien ?

Si l'utilisateur n'a pas de données, proposer un squelette à compléter (voir `references/inventaire-template.md`).

## Étape 2 — Inventorier

Toujours structurer les données AVANT de dessiner. Un diagramme n'est que la projection d'un inventaire cohérent. Sauter cette étape mène à des schémas incomplets ou contradictoires.

Deux tables minimum :

**Table des objets** : `id | nom | type | couche | zone/environnement | responsable | description`

**Table des relations** : `source_id | cible_id | type_relation | protocole | direction | criticité`

Types de relations ArchiMate courants : `serving`, `realization`, `composition`, `flow`, `triggering`, `access` (lecture/écriture de données).

Stocker cet inventaire en Markdown ou CSV dans le dossier de livraison. C'est un livrable en soi — il est souvent réutilisé pour d'autres vues.

## Étape 3 — Modéliser

### Choix du format

| Besoin | Format recommandé |
|---|---|
| Diagramme simple, lecture rapide, intégration Markdown/Notion/GitLab | **Mermaid** |
| Vue ArchiMate stricte, vocabulaire d'architecte, export PNG/SVG propre | **PlantUML + Archimate** |
| Vue applicative en couches (contexte → conteneur → composant) | **Mermaid C4** ou PlantUML C4 |
| Séquence d'appels, flux temporels | **Mermaid sequenceDiagram** |
| Topologie réseau détaillée | **PlantUML nwdiag** ou Mermaid flowchart avec subgraphs |

Lire le fichier de référence correspondant au format choisi :
- `references/mermaid-patterns.md` — patterns Mermaid (flowchart, C4, sequence) adaptés à la carto SI
- `references/plantuml-archimate.md` — stéréotypes ArchiMate et sprites PlantUML
- `references/togaf-archimate.md` — rappel des couches et éléments ArchiMate à utiliser

### Principes de modélisation

Ces principes valent quel que soit le format :

- **Une vue = une question**. Ne pas cumuler infra + applicatif + données sur un seul schéma illisible. Produire plusieurs vues cohérentes qui se répondent.
- **Regrouper par zone** (subgraph Mermaid, rectangle PlantUML) : DMZ, LAN interne, cloud public, SI partenaire, poste utilisateur. La topologie logique doit sauter aux yeux.
- **Nommer les flux** : protocole (HTTPS, SFTP, AMQP), sens, et si possible fréquence ou volumétrie.
- **Couleur = sémantique, pas décoration**. Par exemple : vert = interne, orange = partenaire, rouge = internet ; ou par criticité. Toujours inclure une légende.
- **Cohérence des identifiants** : l'id utilisé dans le diagramme = l'id de l'inventaire. Cela rend les schémas traçables et diffables.

## Étape 4 — Restituer

Structure de livraison recommandée :

```
<nom-projet>/
├── inventaire.md           # ou .csv — objets et relations
├── vue-infrastructure.mmd  # ou .puml
├── vue-applicative.mmd
├── vue-donnees.mmd
├── legende.md              # conventions couleurs/formes
└── synthese.md             # ou .docx — lecture guidée des vues
```

Le document de synthèse doit répondre à : quel est le périmètre ? quelle vue regarder en premier ? quelles décisions/risques le schéma met-il en évidence ? La cartographie n'a de valeur que si quelqu'un sait la lire.

## Rendu des diagrammes

Mermaid et PlantUML sont du **texte** — c'est une force (versionnable, diffable, copiable dans une issue). Pour obtenir une image :

- **Mermaid** : `npx -p @mermaid-js/mermaid-cli mmdc -i diagram.mmd -o diagram.svg` (ou coller dans mermaid.live pour un rendu rapide)
- **PlantUML** : `plantuml diagram.puml` produit le PNG (ou `plantuml -tsvg`)

Si ces outils ne sont pas installés, livrer quand même les sources — l'utilisateur pourra les rendre dans son éditeur (VS Code a d'excellentes extensions).

## Erreurs fréquentes à éviter

- **Schéma-fleuve** : 80 boîtes sur une page A4. Scinder en vues ou introduire des niveaux (C4).
- **Flèches anonymes** : "A → B" sans dire pourquoi. Toujours étiqueter la relation.
- **Confusion des couches** : mélanger serveur physique et application logique dans la même vue. Respecter les couches ArchiMate.
- **Pas de légende** : indéchiffrable pour qui n'était pas dans la réunion de cadrage.
- **Cartographier pour cartographier** : chaque diagramme doit servir une décision (choix d'architecture, analyse d'impact, audit sécu, migration cloud…). Si l'utilisateur ne sait pas à quoi va servir la carto, l'aider à préciser avant de dessiner.

## Fichiers de référence

Lire selon le besoin :

- `references/togaf-archimate.md` — couches, éléments et relations ArchiMate (à lire pour toute vue "entreprise")
- `references/mermaid-patterns.md` — syntaxes Mermaid utiles pour la carto SI
- `references/plantuml-archimate.md` — stéréotypes ArchiMate en PlantUML
- `references/inventaire-template.md` — squelette d'inventaire à remplir avec l'utilisateur
