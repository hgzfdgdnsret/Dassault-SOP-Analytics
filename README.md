# Dassault Aviation — S&OP Analytics 🛩️
> *Simulation d'une architecture Data complète (CRM × ERP × Python × Excel × Power BI) pour piloter la performance commerciale et logistique de l'industrie de la défense.*

---

## 📌 Contexte & Problématique

Ce projet simule une architecture Data orientée **Sales & Operations Planning (S&OP)** chez Dassault Aviation. Face à un carnet de commandes historique à l'export, l'objectif est d'identifier les goulots d'étranglement capacitaires de la Supply Chain, avec un focus sur le marché indien (politique du **"Make in India"**).

**Question clé :** Quels contrats commerciaux sont aujourd'hui financièrement à risque à cause des retards logistiques de notre supply chain indienne (Tata, HAL, Reliance) ?

---

## ⚙️ Stack Technique & Architecture

| Outil | Rôle |
|---|---|
| **Python (Pandas / NumPy)** | Nettoyage des données, EDA, détection d'anomalies |
| **Salesforce CRM** | Suivi du pipeline commercial (Leads, Comptes, Opportunités, Dashboards) |
| **SAP ERP (MM/SD)** | Opérations logistiques (PO, MIGO, VL01N, MIRO, Factures) |
| **Excel (Power Query / TCD)** | Modélisation relationnelle S&OP, reporting financier croisé |
| **Power BI** | Dashboards régionaux, KPI financiers, analyse O2C |

**Structure du dépôt :**

---

## PARTIE 1 — Audit des Retards Fournisseurs & Pipeline CRM
*(Python Pandas — `/notebooks`)*

### 1.1 Données utilisées

- **Retards fournisseurs (MIGO / ERP)** : 760 réceptions analysées, dont **428 en retard** (56 %)
- **Pipeline CRM (Salesforce)** : 1 200 deals en cours — montant total **307 Md€** (tous stades)

### 1.2 Retards par site de production

*(Python Pandas — analyse MIGO croisée avec deux sources internes)*

| Site de production | Retard moyen – Source 1 (j) | Retard moyen – Source 2 (j) |
|---|---|---|
| **Hyderabad** | 30,1 | 29,0 |
| **Bordeaux** | 24,9 | 25,5 |
| **Mérignac** | 21,8 | 19,5 |
| **Nagpur** | 21,8 | 20,5 |
| **Bangalore** | 18,4 | 17,5 |

> ⚠️ Les légères divergences entre les deux sources révèlent un **défaut d'harmonisation des indicateurs** entre les équipes production et logistique.

### 1.3 Pipeline commercial — Répartition par stade

*(Python Pandas — CRM Salesforce)*

| Stade | Montant (Md€) |
|---|---|
| Proposal | 75,4 |
| Closed Won | 63,1 |
| Negotiation | 57,7 |
| Qualification | 44,7 |
| Prospecting | 36,7 |
| Closed Lost | 29,7 |
| **TOTAL** | **307,3** |

**Anomalies CRM détectées (Python)** : 19 deals incohérents, dont :
- `AMOUNT_ZERO_ON_CLOSED_WON` — deals clôturés gagnés avec montant nul
- `STALE_NEGOTIATION` — négociations obsolètes sans mise à jour

### 1.4 Recommandations

- **Gouvernance CRM** : corriger les 19 anomalies, ajouter une règle de validation bloquant toute clôture sans montant positif
- **Scorecard fournisseur** : lier les paiements aux objectifs de ponctualité — action prioritaire sur Hyderabad (cible < 20 j) et Bordeaux (renégociation contrats)
- **Alertes automatiques** : revue direction déclenchée pour toute négociation bloquée > 90 jours

---

## PARTIE 2 — Livraisons ERP, Valeurs Bloquées & Risques Régionaux
*(Excel Power Query / TCD & Power BI — `/dashboards` et `/assets`)*

### 2.1 Sources de données mobilisées

- **Feuil2** *(Excel croisé S&OP)* : tableau croisé des valeurs bloquées par client
- **ERP_Deliveries_VL01N** *(Excel croisé S&OP)* : 389 lignes de livraison — statut, retard, site d'expédition, montant bloqué
- **ERP_SalesOrders_SD** *(Excel croisé S&OP)* : 800 commandes — produits, montants, conditions de paiement, offsets
- **Graphiques régionaux Africa / Europe / Middle East** *(Power BI)* : risque financier, CA gagné, win rates

### 2.2 Clients avec valeurs bloquées

*(Excel croisé S&OP — Feuil2)*

| Client | Valeur bloquée (Md€) |
|---|---|
| Reliance Industries | 7,02 |
| Indian Air Force | 4,56 |
| Indonesian Air Force | 4,05 |
| **TOTAL** | **15,63** |

### 2.3 Livraisons critiques retardées

*(Excel croisé S&OP — ERP_Deliveries_VL01N)*

| Livraison | Client | Produit | Retard | Montant bloqué |
|---|---|---|---|---|
| VL0080000029 | Indonesian Air Force | Rafale F3-R | **365 jours** | 783,5 M€ |
| VL0080000003 | Reliance Industries | Rafale F4 | 60 jours | 882 M€ |
| VL0080000005 | Air France | Falcon 10X | 90 jours | 301,6 M€ |

> Le site de **Nagpur (Inde)** concentre les retards les plus longs. Des retards de 60, 90, 180 et 365 jours sont répertoriés sur l'ensemble du portefeuille VL01N.

### 2.4 Risques financiers par région

*(Power BI — dashboards Africa, Europe, Middle East)*

| Région | CA gagné (Md€) | Win Rate | Risque financier (retards) |
|---|---|---|---|
| Europe | 30 | 70,7 % | Marché principal, concurrence forte (~30 % perte) |
| Moyen-Orient | 11 | 74,7 % | |
| Asie | 7 | n.d. | |
| Afrique | n.d. | 86,7 % | |
| **Toutes régions** | **48** | — | **41,29 Md€** |

> 🔴 **Le risque financier total (41,29 Md€) dépasse le CA gagné cumulé.** L'Inde est simultanément le marché le plus prometteur et le plus exposé.

### 2.5 Recommandations

- **Déblocage progressif** : livraisons partielles + paiements échelonnés pour Reliance, Indian AF, Indonesian AF
- **Offensive logistique Europe** : communiquer sur les plans de réduction de délais pour reconquérir les AO perdus (Belgique, Finlande…)
- **Priorité Inde** : utiliser les obligations offset pour créer des co-entreprises locales de logistique à Nagpur
- **Tableau de bord hebdomadaire** : croiser valeurs bloquées, retards par site et échéances contractuelles — revue mensuelle des 10 plus gros blocages

---

## PARTIE 3 — Audit Commercial & Architecture RevOps
*(Salesforce CRM — `/assets/SalesForce`)*

### 3.1 Architecture Salesforce construite

**Phase 1 — Acquisition Marketing & Gestion des Leads** *(Salesforce — rapport Stacked Bar Chart)*
- Import de **1 200+ Leads** via Data Import Wizard avec nettoyage et mapping manuel (`Lead Source`, `Lead Status`)
- **Insight ROI** : Aero India génère le volume le plus massif (119 leads) mais le **Dubai Airshow** (111 leads) affiche le meilleur taux de conversion qualitatif (*Working / Converted*) → justifie une réallocation du budget événementiel

**Phase 2 — Architecture de Données Relationnelle** *(Salesforce — Object Manager / Dataloader.io)*
- Création de clés primaires `External_ID` sur les objets `Account`, `Contact`, `Opportunity`
- Import séquentiel et rattachement dynamique des Contacts aux Comptes via `External_ID`
- Injection de **1 000 Opportunités** et liaison des `Tasks` commerciales aux `WhoId` (contacts) et `WhatId` (contrats) via Dataloader.io

**Phase 3 — Reporting Avancé & Dashboarding** *(Salesforce — Lightning Dashboard)*
- **"S&OP Executive Control Tower"** : Funnel Chart pipeline, Stacked Bar activités par Sales Rep, KPI financiers
- **Filtre croisé "Neglected Deals"** : rapport *Opportunities WITHOUT Activities* → détection de la faille managériale

### 3.2 Constats chiffrés

*(Salesforce — rapport Neglected Deals / `/assets/SalesForce/salesforce_neglected_deals.png`)*

| Indicateur | Valeur |
|---|---|
| Total leads injectés | 1 200+ |
| Volume par source principale | Aero India : 119 — LinkedIn : 123 — Dubai Airshow : 111 |
| Opportunités actives Q2-2026 | 19 |
| Opportunités sans aucune activité | **19/19 (100 %)** |
| **Pipeline potentiel abandonné** | **6,13 Mds€** |
| Disparité conversion | Aero India : fort volume / faible conversion — Dubai Airshow : meilleur taux |

**Comptes impactés** : Rolls-Royce India, Lockheed Martin India, Indian Air Force, Indian Navy, Egyptian Air Force, Indonesian Air Force, Kuwait Air Force, Singapore Air Force, UAE Air Force, Reliance Defence

**Sources d'acquisition** : Aero India Bangalore, Dubai Airshow, DSEI London, Le Bourget, Euronaval, Embassy Referral, Partner Network, LinkedIn, Government RFP, Diplomatic Channel

### 3.3 Insights clés (par ordre d'importance décroissante)

**1. Pipeline dormant critique — 6,13 Mds€ sans activité** *(Salesforce — Neglected Deals)*
Les 19 opportunités ouvertes (Negotiation, Proposal, Qualification, Prospecting) n'ont aucune `Task` associée depuis leur création. Exemples : *Aviation GE – Indonesia (7 units)* → 275 M€ ; *Royalty FTA – Egypt (26 units)* → 2,3 Mds€ — tous sans suivi.

**2. ROI marketing masqué : volume ≠ performance** *(Salesforce — rapport Lead Source)*
Aero India domine en volume (119 leads) mais le taux de conversion *Working/Converted* est inférieur à Dubai Airshow (111 leads, avancement supérieur). Continuer d'investir dans Aero India sans qualifier les leads = perte budgétaire.

**3. Absence de tracking post-lead** *(Salesforce — absence de workflow)*
Aucune règle n'oblige les commerciaux à créer une `Task` après création d'une opportunité. La direction ne peut pas détecter les deals stagnants avant qu'ils soient perdus. La revue commerciale mensuelle repose sur des données obsolètes.

**4. Probabilités incohérentes avec l'âge des opportunités** *(Salesforce — champ Probability)*
Plusieurs deals affichent 75 % de probabilité avec une date de clôture déjà passée. Exemple : *Training Program – Germany (2 units)* — créé le 09/05/2026, clôture prévue le 04/04/2026, probabilité 75 %. → Forecast et trésorerie S&OP totalement biaisés.

**5. Canaux "External" & "Diplomatic Channel" sous-exploités** *(Salesforce — reporting Lead Source)*
Ces sources ne font jamais l'objet d'une analyse de performance alors qu'elles portent des opportunités à très haute valeur (ex : *Spare Parts Package – Qatar* via Diplomatic Channel).

### 3.4 Recommandations immédiates

1. Forcer la création d'une activité sous **48h** pour toute opportunité en Negotiation/Proposal
2. Réactiver les **19 deals dormants** en affectant un responsable commercial senior
3. Recalibrer les probabilités avec une **matrice stage + âge**
4. Automatiser un **dashboard hebdomadaire "Neglected Deals"** envoyé au VP Sales
5. Réallouer le budget événementiel après analyse ROI complète par Lead Source

---

## PARTIE 4 — Audit Procure-to-Pay (P2P)
*(Excel croisé P2P & SAP MIRO — `/dashboards`)*

**Impact financier total identifié : 48,96 M€**

### 4.1 Synthèse exécutive

L'audit du cycle Procure-to-Pay révèle des défaillances systémiques : le mécanisme de validation des factures (**3-Way Match**) ne filtre pas efficacement les anomalies, générant une perte de trésorerie directe de **48,96 M€**. Le risque se concentre sur des **fournisseurs Tier 1** (Safran, Pratt & Whitney, Thales).

### 4.2 Faille A — Ghost Invoices (Échec du contrôle physique) : 6,93 M€

*(Excel croisé P2P — données SAP MIGO/MIRO)*

Le 3-Way Match (Commande ↔ Réception ↔ Facture) est structurellement contourné :

- **Cas critique Safran Aircraft Engines** (EBELN 409) : 10 unités commandées et réceptionnées — **11 unités payées** → 1 unité "fantôme" à **5,55 M€** payée sans entrée en stock SAP. Révèle un bypass manuel lors de la validation comptable.
- **Volume global** : 11 factures fantômes — total **6,93 M€**

### 4.3 Faille B — Cash Leakage (Dérive des prix négociés) : 42,03 M€

*(Excel croisé P2P — Price Variance)*

51 factures honorées au-delà des conditions négociées par le service Achats :

| Fournisseur | Surfacturation (M€) |
|---|---|
| Pratt & Whitney | 9,94 |
| Elbit Systems India | 8,24 |
| Safran Aircraft Engines | 6,07 |

### 4.4 Insights stratégiques

1. **Effet "Too Big to Fail"** : les anomalies se concentrent sur les fournisseurs ultra-critiques (Safran, P&W) car la validation s'effectue sous pression de production (risque de rupture d'assemblage). Ces fournisseurs imposent un rapport de force favorable.
2. **Défaillance ERP** *(SAP MIRO)* : les seuils de tolérance sont inopérants. Un ERP correctement paramétré doit bloquer automatiquement toute facture présentant un écart > 5,5 M€.
3. **Érosion de la performance Achats** : les gains négociés en amont sont annulés en phase de paiement. Le processus O2C des fournisseurs est techniquement plus robuste que notre contrôle P2P.

### 4.5 Plan d'action (Roadmap ROI)

**Phase 1 — Cash Recovery immédiat (< 15 jours)**
- Campagne de recouvrement auprès du Top 5 fournisseurs → exiger des Credit Memos couvrant les 48,96 M€
- Suspension temporaire des échéances Pratt & Whitney et Safran dans l'attente de régularisation

**Phase 2 — Verrouillage systémique (< 3 mois)** *(SAP MIRO — Hard Stop)*
- Reconfiguration stricte des clés de tolérance ERP : blocage automatique si écart > 0,5 % ou > 1 000 €
- Workflow d'escalade : double validation obligatoire (Acheteur + Contrôleur de Gestion) pour tout déblocage hors tolérance

**Phase 3 — Business Review Fournisseurs (Moyen terme)**
- Intégration du KPI **"Invoice Accuracy Rate"** dans les Quarterly Business Reviews
- Utilisation de ce taux comme malus lors des futures négociations contractuelles

---

## PARTIE 5 — Revue Stratégique Globale : Analyse Croisée P2P × CRM × O2C
*(Excel croisé Case / Power BI — `/dashboards` et `/assets`)*

### 5.1 Méthodologie

| Phase | Périmètre | Outil | Résultat |
|---|---|---|---|
| Phase 1 | Audit P2P | Excel croisé P2P / SAP MIRO | 48,96 M€ d'anomalies (Ghost Invoices + Price Variance) |
| Phase 2 | Audit fournisseurs | Excel croisé Case / CRM | Corrélation litiges qualité ↔ défaillances facturation |
| Phase 3 | Audit O2C | Power BI | Cash at Risk : 24 Md€ sur créances souveraines |

### 5.2 Analyse croisée des données

**A. Performance Opérationnelle Fournisseurs — P2P vs CRM** *(Excel croisé Case)*

Corrélation structurelle identifiée : le Top 3 des fournisseurs avec les écarts financiers les plus élevés (*Pratt & Whitney, Elbit Systems, Safran*) est également le Top 3 des acteurs avec le plus de litiges qualité dans le CRM.

> *Note méthodologique* : le volume brut de 30 571 incidents (Cases) est la résultante d'un **produit cartésien** lors de la jointure des tables (incidents × lignes commandes/articles). Le volume absolu est artificiellement dilaté, mais la **distribution proportionnelle confirme la défaillance opérationnelle** des fournisseurs Tier 1.

**B. Exposition au Risque Client — O2C** *(Power BI — dashboard O2C)*

- CA global analysé : **117 Md€**
- **Cash at Risk : 24 Md€** (20,63 % du CA en statut "Overdue")
- Créances douteuses concentrées sur des comptes souverains : Croatian Air Force, UAE Air Force…

### 5.3 Insights stratégiques

1. **Effet Ciseaux sur le BFR** : en amont, les décaissements P2P sont gonflés par les anomalies fournisseurs ; en aval, les encaissements O2C sont bloqués par 24 Md€ de créances échues → déséquilibre de trésorerie structurel.
2. **Corrélation Qualité / Conformité** : les fournisseurs Tier 1 concentrent simultanément les risques opérationnels (litiges CRM) et financiers (surfacturation P2P).
3. **Données en Silo** : l'absence d'interface native ERP ↔ CRM empêche tout contrôle en temps réel. L'audit a nécessité une agrégation manuelle multi-sources.

### 5.4 Plan d'action global

| Priorité | Action | Délai | Impact attendu |
|---|---|---|---|
| 1 | Débloquer 15,6 Md€ (Reliance, Indian AF, Indonesian AF) | 90 jours | Réduction du risque + amélioration trésorerie |
| 2 | Éliminer tous les retards > 180 jours | 6 mois | Zéro retard critique |
| 3 | Corriger anomalies CRM + déployer scorecard fournisseur | 3–6 mois | Fiabilité du pilotage S&OP |
| 4 | Hard Stop ERP (SAP MIRO) + workflow double validation | 3 mois | Fin des Ghost Invoices et Cash Leakage |
| 5 | Task force recouvrement souverain (24 Md€ O2C) | 6 mois | Réduction du Cash at Risk |
| 6 | Refonte contractuelle SLA Tier 1 | Long terme | Pénalités indexées sur Invoice Accuracy Rate |

> **Couverture financière recommandée** : provisionner 5 % du risque total (**~2 Md€**) pour absorber les pénalités et négocier des déblocages anticipés.

---

## Conclusion Exécutive

| Indicateur | Valeur |
|---|---|
| Pipeline commercial total | **307 Md€** |
| CA gagné | **48 Md€** |
| Risque financier (retards logistiques) | **41,29 Md€** |
| Valeurs bloquées (3 clients) | **15,63 Md€** |
| Anomalies P2P (Ghost Invoices + Price Variance) | **48,96 M€** |
| Cash at Risk O2C (créances échues) | **24 Md€** |
| Pipeline CRM dormant (0 activité) | **6,13 Mds€** |

Dassault Aviation dispose d'un pipeline exceptionnel, mais les retards logistiques, les défaillances du contrôle P2P et l'absence de tracking commercial génèrent un risque financier global dépassant le CA gagné. L'Inde est à la fois le marché le plus prometteur et le plus exposé. La mise en œuvre coordonnée des actions décrites ci-dessus — gouvernance des données, réduction des retards critiques, Hard Stops ERP, recouvrement souverain — permettra de réduire le risque, d'assainir la trésorerie et de renforcer la compétitivité sur tous les marchés clés.

---

## 🛠️ Reproduire le projet

```bash
# 1. Cloner le repository
git clone https://github.com/hgzfdgdnsret/Dassault-SOP-Analytics.git

# 2. Régénérer les datasets simulés
python scripts/generate_crm.py
python scripts/generate_erp.py

# 3. Lancer l'EDA et la détection d'anomalies
jupyter notebook notebooks/

# 4. Consulter les dashboards S&OP
# → /dashboards (Excel Power Query / TCD)

# 5. Consulter les assets visuels
# → /assets/SalesForce (captures Salesforce CRM)
# → /assets/excel (captures Excel / Power BI)
```

---

*Stack : Python (Pandas, NumPy) · Salesforce CRM (Data Import Wizard, Dataloader.io, Lightning Dashboard) · SAP ERP (MM/SD : MIGO, VL01N, MIRO) · Excel (Power Query, TCD) · Power BI*
