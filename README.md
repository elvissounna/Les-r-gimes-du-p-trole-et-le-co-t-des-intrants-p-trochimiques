# Les régimes du pétrole et le coût des intrants pétrochimiques
# Transmission des chocs pétroliers aux intrants d'un manufacturier de revêtements de sol

Analyse économétrique et empirique de la transmission des variations du pétrole brut (WTI en dollars canadiens) vers les coûts des intrants pétrochimiques de l'industrie des revêtements de sol au Canada (2010–2026), incluant une validation hors échantillon lors du choc géopolitique de février–mars 2026 (guerre en Iran / détroit d'Ormuz).

Ce dépôt contient le carnet Jupyter (`transmission_chocs_petroliers_revetements_sol.ipynb`) accompagnant le rapport dédié aux décisions d'approvisionnement et de gestion des coûts en période de choc énergétique.

---

## Problématique

Un manufacturier de revêtements de sol dépend fortement de dérivés pétrochimiques : polypropylène, nylon, polyester, latex. Lorsqu'un choc géopolitique ou macroéconomique fait bondir le prix du baril :
1. **Avec quel délai** cette hausse se répercute-t-elle sur chaque famille d'intrants ?
2. **Avec quelle intensité** (élasticité) ?
3. **Quelle fenêtre d'action** reste-t-il à une équipe d'achats avant que la hausse ne frappe la facturation ?

---

## Principaux résultats

| Intrant (Code NAPCS) | Décalage optimal | Corrélation max (2010–2026) | Corrélation à 1 mois (depuis 2021) | Élasticité cumulée (sur 4 mois, depuis 2021) | Posture achats suggérée |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Produits pétrochimiques** `[26321]` | **1 mois** | `0,50` | `0,59` | `0,54` (+5,4 % pour +10 % WTI) | Indicateur avancé fiable. Déclencher une revue des prix dès un bond mensuel du WTI > +10 %. |
| **Résines plastiques** `[281]` | **1 à 2 mois** | `0,43` | `0,66` | `0,70` (+7,0 % pour +10 % WTI) | Même seuil d'alerte, mais calibrer les scénarios de risque sur la **volatilité récente (post-2020)** et non sur la moyenne historique. |
| **Caoutchouc synthétique / latex** `[28211P]` | **2 mois** | `0,23` | `0,21` | `0,15` (+1,5 % pour +10 % WTI) | Fenêtre de réaction plus longue ; lien modéré à surveiller sans surréagir. |
| **Fibres synthétiques** `[28221]` | *Aucun* | `0,13` | `-0,06` | *Non significatif* | Ne pas piloter sur le cours du pétrole ; surveiller les capacités de production, la demande et le taux de change. |

### Faits saillants de l'étude
* **Changement de régime structurel (février 2020) :** Un modèle de Markov caché (`GaussianHMM`) identifie un basculement durable des résines plastiques vers un régime de haute volatilité depuis février 2020. L'écart-type des variations mensuelles a été multiplié par **4,8** (passant de `1,18 %` sur 2010–2020 à `5,74 %` sur 2020–2026).
* **Resserrement du lien pétrole → résines :** La corrélation à un mois des résines avec le WTI en $ CAD est passée de `0,31` avant 2020 à `0,56` sur 2021–2025, puis à `0,66` en incluant 2026.
* **Validation hors échantillon (choc d'Ormuz, 2026) :** Estimé uniquement sur les données jusqu'en décembre 2025, le modèle à retards distribués prédit avec exactitude la trajectoire des produits pétrochimiques (+25,7 % prévus contre +26,4 % observés entre mars et juin 2026). Pour les résines, il capte le **calendrier et la direction** (corrélation `0,86`), mais sous-estime l'amplitude (+18 % prévus contre **+54 % observés**), confirmant l'effet d'amplification propre au régime post-2020.
* **Règle d'alerte opérationnelle :** Historiquement, après un mois où le WTI en $ CAD grimpe de plus de **10 %** (17 occurrences sur 199 mois), les produits pétrochimiques augmentent d'au moins 2 % dans les deux mois suivants dans **82 % des cas** (contre 45 % en temps normal), et les résines dans **53 % des cas** (contre 21 % en temps normal).

---

## Méthodologie et structure du notebook

Le carnet `transmission_chocs_petroliers_revetements_sol.ipynb` est structuré en 11 sections reproductibles :

1. **Préparation et environnement** : Configuration graphique et chargement des librairies.
2. **Construction et audit des données (`2010-01` à `2026-08`, 200 mois)** : Fusion et vérification d'intégrité entre les séries brutes de Statistique Canada et les données macro-financières, puis passage en variations logarithmiques mensuelles.
3. **Vue d'ensemble historique** : Visualisation comparée de l'indice de risque géopolitique et des indices de prix (base 100 = janvier 2020) à travers trois épisodes majeurs (COVID-19 en 2020, invasion de l'Ukraine en 2022, guerre en Iran / détroit d'Ormuz en 2026).
4. **Détection de régimes (`GaussianHMM`)** : Segmentation non supervisée des états de volatilité sélectionnée par critère BIC.
5. **Analyse des décalages et robustesse temporelle** : Corrélations croisées ($L = 0 \dots 6$ mois), sous-échantillonnage (`Avant 2020`, `Depuis 2020`, `Depuis 2021`, `2021–2025`), fenêtre glissante de 36 mois et tests de **causalité au sens de Granger**.
6. **Estimation des élasticités** : Régressions à retards distribués (MCO avec erreurs standard robustes HAC de Newey-West).
7. **Étude de cas comparée (2022 vs 2026)** : Analyse de l'effet de ciseaux entre la flambée des intrants (+61 % au sommet pour les résines en mai 2026) et l'inertie du prix du produit fini (tapis, `-2 %` jusqu'en juin avant un ajustement de `+8 %` en juillet).
8. **Validation hors échantillon (Out-of-Sample)** : Prévision des variations de janvier à août 2026 à partir d'un modèle entraîné strictement jusqu'en décembre 2025.
9. **Règle d'alerte achats** : Calcul des probabilités conditionnelles de hausse des intrants suite à un choc mensuel du baril (> +5 % et > +10 %).
10. **Implications opérationnelles et limites méthodologiques** : Précautions d'interprétation (corrélation vs causalité, indices de branche vs prix contractuels, effet taux de change).
11. **Export des résultats** : Génération automatique des graphiques haute résolution (`figures/`) et sauvegarde des métriques clés (`resultats_cles.json`).

---

## Sources de données

* **Statistique Canada — Tableau 18-10-0266-01** : *Indice des prix des produits industriels (IPPI)*, mensuel, base janvier 2020 = 100 (codes NAPCS `[281]`, `[28221]`, `[28211P]`, `[26321]`, `[23211]`).
* **FRED (Federal Reserve Bank of St. Louis)** : Prix au comptant du gaz naturel Henry Hub (`MHHNGSP`), taux de change CAD/USD (`DEXCAUS`), et indices PPI américains de référence (`PCU325211325211`, `PCU325212325212`).
* **U.S. Energy Information Administration (EIA) / Base GWP** : Cours au comptant du pétrole brut *West Texas Intermediate* (WTI) et indice de risque géopolitique.

---

## Installation et exécution

### Prérequis
* Python 3.9+
* Fichiers de données dans le répertoire racine : `intrants_oil_complet.csv`, `statcan_ippi_brut.csv`, `oil_clean.csv`.

### Installation des dépendances
```bash
pip install numpy pandas matplotlib statsmodels hmmlearn

