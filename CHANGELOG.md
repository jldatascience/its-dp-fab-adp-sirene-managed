# Changelog

Toutes les évolutions notables du dépôt `its-dp-fab-adp-sirene-managed` (configuration de déploiement, pipelines,
documentation) sont consignées ici, de la plus récente à la plus ancienne.

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/). Ce dépôt n'est pas versionné
par tag : chaque entrée est datée (`## AAAA-MM-JJ`). Les évolutions du socle commun sont décrites
dans le `CHANGELOG.md` de `its-dp-fab-cicd-common`, versionné par tag `vX.Y.Z`.

## 2026-11-11

### Changed

- Fichiers de configuration issus d'une copie du dépôt Framework, réécrits pour l'ADP Sirene
  (socle `its-dp-fab-cicd-common`, tag `v1.5.0`, catégorie `adp` ; modèle : dépôt ADP Alexandrie,
  aligné le 01/11/2026 sur les autres consommateurs) :
  - `CICD/deployment/config.yml` : workspaces `FR - DP - <ENV> - FAB - ADP - Sirene - MANAGED`
    pour DEV, ITG, PPR et PRD (confirmés le 11/11/2026, tiret simple),
    `repository_directory: "../../fabric"`, `item_types_in_scope` vide, clé `core.parameter`
    retirée, `folder_exclude_regex` et `features` retirés (aucun dossier exclu).
  - `CICD/deployment/parameter.yml` : 56 règles Framework retirées ; aucune règle (en-tête :
    les deux familles de règles admises pour un ADP).
  - `CICD/deployment/dependencies.yml` : `role: consumer`, `category: adp`,
    `framework.required: true` (workspaces Framework, `items: []`, `ado_gate`) ; connexion
    `DP-Fabric-Global-APR` et Variable Library `vl_core_config` du Framework retirées ;
    `guid_policy.forbidden_prefixes` : workspaces ADP Sirene DEV, ITG, PPR, PRD et Framework DEV,
    absents du registre du socle v1.5.0.
  - `CICD/pipelines/CD.yml` : `repository: its-dp-fab-adp-sirene-managed`, `role: consumer`,
    porte Framework `ado` ; source CI `its-dp-fab-adp-sirene-managed-ci`, reprise de l'ancienne CD ;
    remplace l'ancienne CD (ITG → PPR → PRD enchaînés sans approbation, `Fabric-CD-Secrets`,
    fabric-cicd 1.2.0, `deployment/deploy.py` avec ID en dur).
  - `CICD/pipelines/CI.yml` : template KPMG conservé ; déclencheurs `develop`, `release/*`, `main`
    (au lieu de `feature/its/411581_industrialisation_chaine_cicd`) ; SonarQube
    `Discovery_DP-FAB-ADP-Sirene-Managed`, repris de l'ancienne CI ; filtre `"!/*.json"` entre
    guillemets (l'ancien `!**/*.json` non quoté était une étiquette YAML).
  - `CICD/pipelines/PR-Validation.yml` : en-tête Sirene, contenu inchangé.
  - `CICD/ref_dynamique/01_inventaire_github.py` à `04_recoupement.py` : la copie de l'outillage
    Framework/MDS est remplacée par l'outillage des consommateurs ADP (dépôt seul, une branche,
    sorties hors du dépôt, activités imbriquées, références Framework légitimes) ; `01` classe
    aussi les GUID avec `guid_policy` de `dependencies.yml`.
  - `CICD/ref_dynamique/templates/config.yml` et `parameter.yml` : modèles ADP au format du socle
    (noms de workspaces, aucun GUID, aucune clé en minuscules).
  - `.gitignore` : convention des dépôts consommateurs (identique à MDS, Alexandrie, UCS et BDP).
  - README réécrit au format des autres consommateurs.

### Removed

- `fabric/test.md` (contenu « test », référencé par aucun pipeline ni script ; suppression validée).

### Added

- `fabric/.gitkeep` : dossier des items suivi par Git avant la première synchronisation.

### Vérifié

- `yamllint` (règles du socle) sur `CICD/deployment` et `CICD/pipelines` : sans erreur.
- `python -m compileall CICD/ref_dynamique` : sans erreur.
- `deploy.py validate` (socle `v1.5.0`) : code 4 attendu (« Aucun item Fabric »). Sur une copie de
  travail munie d'un item fictif : 0 erreur, `repository_directory` résolu vers `fabric/`, matrice
  `adp` acceptée.

### À confirmer

- Workspace DEV comme source des items ; Git Integration DEV branchée sur le dossier `fabric`.
- Types d'items, règles, `framework.items`, connexions Fabric, Variable Libraries, `guid_policy`
  (sandbox, `allowed`) et `data_dependencies`, après inventaire (scripts 01 à 04). L'ancien
  `deploy.py` déclarait un lakehouse `lkh_storage_<ENV>` par environnement : nom à vérifier.
- Organisation et service connection GitHub ; noms des 3 service connections ARM ITG, PPR et PRD
  (création prévue).
- Casse exacte du pipeline CI dans Azure DevOps ; nom exact de la CD Framework.
- Approbations sur les Environments ADO `fabric-ppr` et `fabric-prd`.
- Point V-01 : le filtre `contents: "!/*.json"` de l'artefact CI ne doit pas exclure les fichiers d'items.
- Ajout des workspaces ADP Sirene et Framework DEV au registre du socle, au prochain tag.
