# Template d'inventaire

À remplir AVANT de dessiner. L'inventaire est le livrable pivot : il sert aux schémas, aux audits, aux analyses d'impact, et se maintient dans le temps.

## Table des objets

| id | nom | type | couche | zone | responsable | criticité | description |
|----|-----|------|--------|------|-------------|-----------|-------------|
| APP001 | CRM Salesforce | Application Component | Application | SaaS | Direction commerciale | 3 | Gestion clients B2B |
| APP002 | ERP SAP ECC | Application Component | Application | LAN interne | DAF | 4 | Finance, RH, achats |
| DB001 | pg-crm-prod | System Software / DB | Technology | VLAN DB | DSI Ops | 4 | PostgreSQL 15, données CRM |
| SRV001 | app-crm-prod-01 | Node | Technology | VLAN Back | DSI Ops | 3 | VM Linux, 8 vCPU, 32 Go |
| DO001 | Fiche client | Data Object | Application | — | Direction commerciale | 4 | Référentiel client or |

### Types autorisés (aligné ArchiMate)

- Couche Application : `Application Component`, `Application Service`, `Application Interface`, `Data Object`, `Application Collaboration`
- Couche Technology : `Node`, `Device`, `System Software`, `Network`, `Technology Service`, `Artifact`
- Couche Business (si utilisé) : `Business Actor`, `Business Process`, `Business Service`

### Échelle de criticité (exemple)

1 = non critique · 2 = important · 3 = critique métier · 4 = vital (arrêt = perte significative) · 5 = essentiel vital (sécurité, conformité, OIV)

Adapter à l'échelle de l'organisation si elle existe déjà (ex : DICP ANSSI, échelle interne).

## Table des relations

| source_id | cible_id | type | protocole | fréquence | direction | criticité | description |
|-----------|----------|------|-----------|-----------|-----------|-----------|-------------|
| APP001 | APP002 | Flow | SFTP | quotidien 2h | unidir | 3 | Export clients CRM → ERP |
| APP002 | DB001 | Access_rw | JDBC | continu | bidir | 4 | Lectures/écritures données CRM |
| APP001 | DO001 | Realization | — | — | — | — | Le CRM réalise/maintient la fiche client |
| APP001 | SRV001 | Assignment | — | — | — | — | Application déployée sur serveur |

### Types de relations

Reprendre strictement ArchiMate : `Composition`, `Aggregation`, `Realization`, `Serving`, `Used By`, `Flow`, `Triggering`, `Access_r`, `Access_w`, `Access_rw`, `Assignment`.

## Bonnes pratiques

- **Identifiants stables** : `APP001`, `SRV001` etc. Ne jamais réutiliser un id. Les schémas doivent référencer ces ids pour être traçables.
- **Un seul fichier source** : l'inventaire est *la* source de vérité ; les diagrammes sont régénérés à partir de lui. Si les deux divergent, c'est l'inventaire qui gagne.
- **Champ "responsable" obligatoire** : une carto sans propriétaires identifiés est inexploitable pour la suite (audit, décision, migration).
- **Préférer CSV si plus de ~50 lignes** : plus maniable que Markdown et ouvrable dans Excel.

## Modèle CSV prêt à l'emploi

### `objets.csv`

```csv
id,nom,type,couche,zone,responsable,criticite,description
APP001,CRM Salesforce,Application Component,Application,SaaS,Direction commerciale,3,Gestion clients B2B
```

### `relations.csv`

```csv
source_id,cible_id,type,protocole,frequence,direction,criticite,description
APP001,APP002,Flow,SFTP,quotidien 2h,unidir,3,Export clients CRM vers ERP
```

## Conversion inventaire → diagramme

Une fois l'inventaire stabilisé, la traduction en Mermaid est mécanique :

- chaque ligne d'`objets.csv` devient un nœud (`id[nom]` ou `id[(nom)]` pour une BDD)
- chaque ligne de `relations.csv` devient une flèche `source --> |protocole - fréquence| cible`
- les zones deviennent des `subgraph`

Si l'utilisateur a un inventaire, proposer d'écrire un petit script Python (pandas) qui génère le `.mmd` à partir des CSV. Cela évite la ressaisie et garde cohérent les deux artefacts.
