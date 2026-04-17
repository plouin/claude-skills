# Patterns Mermaid pour la cartographie SI

Mermaid est idéal pour des diagrammes lisibles, versionnables et intégrables dans Markdown (GitLab, GitHub, Notion, Confluence récent).

## Vue applicative — flowchart avec zones

Le `subgraph` matérialise les zones (DMZ, LAN, cloud, partenaire). C'est l'outil le plus important pour une carto lisible.

```mermaid
flowchart LR
    subgraph Internet
        U[Utilisateur externe]
    end
    subgraph DMZ
        WAF[WAF]
        PORTAIL[Portail web]
    end
    subgraph LAN["LAN interne"]
        API[API Client]
        CRM[(CRM)]
        ERP[(ERP)]
    end
    subgraph Partenaire
        BANQUE[SI Banque]
    end

    U -->|HTTPS| WAF
    WAF --> PORTAIL
    PORTAIL -->|REST /clients| API
    API -->|SQL| CRM
    API -->|SOAP| ERP
    ERP -->|SFTP nightly| BANQUE

    classDef interne fill:#cfe8ff,stroke:#1f5fb3
    classDef externe fill:#ffd9b3,stroke:#c46a00
    class API,CRM,ERP,PORTAIL,WAF interne
    class BANQUE,U externe
```

Conventions retenues :
- forme `[(...)]` pour une base de données
- forme `[...]` pour un composant applicatif
- étiquette systématique sur les flux avec protocole
- `classDef` pour coloriser par zone de confiance

## Vue infrastructure — flowchart avec nœuds et VLAN

```mermaid
flowchart TB
    subgraph DC1["Datacenter Paris"]
        direction TB
        subgraph VLAN_FRONT["VLAN Front 10.10.1.0/24"]
            LB[Load balancer<br/>HAProxy]
            WEB1[web-01]
            WEB2[web-02]
        end
        subgraph VLAN_BACK["VLAN Back 10.10.2.0/24"]
            APP1[app-01]
            APP2[app-02]
        end
        subgraph VLAN_DB["VLAN DB 10.10.3.0/24"]
            DB1[(pg-primary)]
            DB2[(pg-replica)]
        end
    end

    LB --> WEB1 & WEB2
    WEB1 & WEB2 --> APP1 & APP2
    APP1 & APP2 --> DB1
    DB1 -. réplication async .-> DB2
```

Astuces :
- `direction TB` dans un subgraph pour contrôler l'orientation locale
- `A & B --> C & D` pour matrices de connexions
- `-. label .->` pour flèches pointillées (réplication, fallback)

## Vue données — flux et lineage

```mermaid
flowchart LR
    SRC1[(CRM)]:::source
    SRC2[(ERP)]:::source
    ETL{{ETL nightly}}
    DWH[(Data Warehouse)]:::dwh
    BI[Tableau BI]
    ML[Modèle churn]

    SRC1 -->|extract daily| ETL
    SRC2 -->|extract daily| ETL
    ETL -->|load| DWH
    DWH --> BI
    DWH --> ML

    classDef source fill:#ffe4b5
    classDef dwh fill:#b5e4c8
```

Notation `{{...}}` pour un traitement/job ; distincte des sources et sinks. Étiqueter la fréquence des flux (daily, real-time, on-demand) — c'est presque toujours la question qu'on pose.

## Vue C4 niveau Contexte

Mermaid supporte C4 nativement (encore en bêta mais utilisable) :

```mermaid
C4Context
    title Contexte — Plateforme e-commerce
    Person(client, "Client", "Achète en ligne")
    Person(admin, "Administrateur", "Gère catalogue")
    System(ecom, "Plateforme e-commerce", "Site + back-office")
    System_Ext(pay, "Prestataire paiement", "Stripe")
    System_Ext(erp, "ERP interne", "SAP")

    Rel(client, ecom, "Navigue, commande", "HTTPS")
    Rel(admin, ecom, "Administre", "HTTPS")
    Rel(ecom, pay, "Autorise paiement", "API REST")
    Rel(ecom, erp, "Pousse commandes", "AMQP")
```

Utiliser C4 quand il faut montrer les acteurs humains et les systèmes externes, pas seulement la tuyauterie interne.

## Vue séquence — appel d'API ou scénario

```mermaid
sequenceDiagram
    actor U as Utilisateur
    participant FE as Frontend
    participant API as API Gateway
    participant SVC as Service Commande
    participant DB as PostgreSQL

    U->>FE: Valider panier
    FE->>API: POST /orders
    API->>SVC: createOrder()
    SVC->>DB: INSERT order
    DB-->>SVC: id
    SVC-->>API: 201 Created
    API-->>FE: 201 Created
    FE-->>U: Confirmation
```

Utile pour documenter un flux transactionnel critique ou une chaîne de responsabilité.

## Rendu en image

```bash
# Installation one-shot et rendu SVG
npx -p @mermaid-js/mermaid-cli mmdc -i vue-applicative.mmd -o vue-applicative.svg

# PNG avec fond blanc
npx -p @mermaid-js/mermaid-cli mmdc -i vue.mmd -o vue.png -b white
```

Mermaid rend aussi directement dans GitLab/GitHub Markdown (bloc ```mermaid```), ce qui est souvent suffisant — pas besoin de produire un PNG.

## Pièges Mermaid

- Les `(` `)` `[` `]` dans les labels cassent le parser. Les échapper avec des guillemets : `A["Libellé (avec parenthèses)"]`
- Les diagrammes avec >50 nœuds deviennent souvent illisibles. Scinder en plusieurs vues.
- L'orientation `LR` (gauche→droite) est presque toujours plus lisible que `TD` pour une vue applicative avec flux.
