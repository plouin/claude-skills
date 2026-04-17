# TOGAF / ArchiMate — rappel utile pour la carto SI

Ce document est une antisèche. Pour la norme complète, voir The Open Group.

## Les 3 couches retenues pour ce skill

ArchiMate définit plus de couches (Strategy, Motivation, Physical…) mais pour une cartographie SI classique on se concentre sur 3 :

| Couche | Objet | Exemple |
|---|---|---|
| **Business** (optionnel) | Processus, acteurs, services métier | "Processus de facturation", "Commercial" |
| **Application** | Composants applicatifs, services, interfaces, data objects | "SAP ECC", "API Client", "Fichier client" |
| **Technology** | Nœuds, devices, réseaux, artifacts déployés | "Serveur Linux prod", "Kubernetes", "VLAN DMZ" |

## Éléments ArchiMate essentiels

### Application Layer

- **Application Component** : module applicatif déployable autonome. Le plus fréquent.
- **Application Service** : capacité exposée (souvent derrière une API).
- **Application Interface** : point d'exposition (REST endpoint, IHM, file d'attente).
- **Data Object** : information manipulée (≠ base de données, c'est la donnée logique).
- **Application Collaboration** : agrégat de composants qui coopèrent.

### Technology Layer

- **Node** : ressource d'exécution (serveur physique/virtuel, conteneur, cluster).
- **Device** : matériel physique (routeur, baie, poste).
- **System Software** : OS, SGBD, middleware.
- **Communication Network** : LAN, VLAN, VPN, internet.
- **Technology Service** : service technique exposé (stockage, monitoring).
- **Artifact** : fichier/binaire déployé (WAR, image Docker, script).

## Relations ArchiMate (celles qu'on utilise vraiment)

| Relation | Notation | Usage |
|---|---|---|
| **Composition** | ◆— | "X est composé de Y" (tout/partie fort) |
| **Aggregation** | ◇— | "X regroupe Y" (tout/partie faible) |
| **Realization** | ⟶ ligne pointillée triangle | "X réalise/implémente Y" (ex : composant réalise un service) |
| **Serving / Used By** | ⟶ | "X rend service à Y" (sens du service, pas de l'appel) |
| **Flow** | ⟶⟶ | "transfert d'information ou de valeur entre X et Y" (avec étiquette !) |
| **Triggering** | ⟶ pleine avec croix | "X déclenche Y" |
| **Access** | ─── avec flèche selon R/W | "X accède à la donnée Y" (lecture, écriture, les deux) |
| **Assignment** | — point plein | "X est alloué à Y" (composant applicatif assigné à un nœud) |

Piège courant : confondre **Serving** (qui rend service à qui) et **Flow** (qui transmet quoi). Serving est statique/structurel, Flow est dynamique/informationnel.

## Vues TOGAF classiques

TOGAF définit des points de vue normalisés. Les plus demandés en cartographie opérationnelle :

- **Vue de contexte applicatif** (Application Cooperation) : quelles applis dialoguent avec quelles autres, via quels flux ?
- **Vue de déploiement** (Infrastructure Usage) : quelles applis tournent sur quels nœuds, dans quelles zones ?
- **Vue des flux de données** (Information Structure / Flow) : qui produit, qui consomme, quel référentiel fait foi ?
- **Vue de contexte métier→SI** (Application Usage) : quel processus métier s'appuie sur quelles applis ?

Produire une vue dédiée par question. Ne pas essayer de tout dire sur un seul schéma.

## Couleurs conventionnelles ArchiMate

Les outils ArchiMate (Archi, BiZZdesign) utilisent un code couleur par couche. Si tu génères du PlantUML ou du Mermaid, reproduire cette convention aide les lecteurs habitués :

- **Business** : jaune
- **Application** : bleu
- **Technology** : vert
- **Motivation / Strategy** (rare en carto SI) : violet / rouge-orange

En cartographie de sécurité (inspiration ANSSI), on superpose souvent un code par criticité ou par zone de confiance, ce qui écrase le code par couche. Choisir un seul axe de coloration par schéma et l'expliciter dans la légende.
