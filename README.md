# Dassault Aviation - Sales & Operations Planning (S&OP) Analytics 🛩️
## 📌 Contexte du Projet
Ce projet est une simulation technique d'une architecture Data orientée S&OP (Sales & Operations Planning) chez Dassault Aviation. 
Face à un carnet de commandes historique à l'export, l'objectif est d'identifier les goulots d'étranglement capacitaires de la Supply Chain. Plus précisément, le projet se concentre sur le marché indien (politique du "Make in India") en croisant les données commerciales (CRM) avec les données de production (ERP) pour obtenir une vue exécutive complète du risque financier.
## 🎯 Problématique
La politique de compensation industrielle ("Make in India") nous oblige à utiliser des fournisseurs locaux (Tata, HAL, Reliance). 
**Question clé :** Quels contrats commerciaux sont aujourd'hui financièrement à risque à cause des retards logistiques de notre supply chain indienne ?
## ⚙️ Stack Technique & Architecture
L'analyse repose sur la fusion de deux environnements de données distincts :
- **CRM (type Salesforce) :** Suivi du pipeline (Comptes, Leads, Opportunités).
- **ERP (type SAP MM/SD) :** Opérations logistiques (Commandes d'achats, Mouvements MIGO, Livraisons, Factures).
- **Python (Pandas) :** Nettoyage des données, EDA (Analyse Exploratoire) et détection d'anomalies.
- **Excel (Power Query / TCD) :** Modélisation relationnelle S&OP et reporting financier.
---
# Dassault Aviation – Audit des retards, risques financiers et performance commerciale

## Vue d’ensemble

Ce dépôt contient l’analyse croisée de deux jeux de données opérationnels et commerciaux de Dassault Aviation.  
L’objectif est d’identifier les causes et les impacts des retards (fournisseurs et livraisons), de quantifier les risques financiers associés, et de proposer des stratégies concrètes pour y remédier.

Deux approches complémentaires sont présentées :

- **Partie 1 – Analyse Python** : audit des retards fournisseurs (données MIGO) et du pipeline CRM (Salesforce).
- **Partie 2 – Analyse Excel / Power BI** : examen des livraisons ERP, des valeurs bloquées par client et des risques financiers par région.

---

## Partie 1 – Analyse Python : retards fournisseurs et pipeline CRM

### 1.1 Données utilisées

- **Retards fournisseurs (MIGO)** : 760 réceptions, dont 428 en retard.
- **Pipeline CRM (Salesforce)** : 1 200 deals en cours, pour un montant total de 307 Md€ (tous stades confondus).

### 1.2 Principaux constats

#### Retards par site de production

| Site       | Retard moyen (jours) – source 1 | Retard moyen (jours) – source 2 |
|------------|--------------------------------|--------------------------------|
| Hyderabad  | 30,1                           | 29,0                           |
| Bordeaux   | 24,9                           | 25,5                           |
| Mérignac   | 21,8                           | 19,5                           |
| Nagpur     | 21,8                           | 20,5                           |
| Bangalore  | 18,4                           | 17,5                           |

*Deux sources internes donnent des valeurs légèrement différentes, signe d’un défaut d’harmonisation des indicateurs.*

#### Pipeline commercial

- **Proposal** : 75,4 Md€  
- **Negotiation** : 57,7 Md€  
- **Closed Won** : 63,1 Md€  
- **Closed Lost** : 29,7 Md€  
- **Qualification** : 44,7 Md€  
- **Prospecting** : 36,7 Md€  

**Anomalies détectées :** 19 deals incohérents, dont des `AMOUNT_ZERO_ON_CLOSED_WON` (montant nul sur des deals censés être gagnés) et un `STALE_NEGOTIATION` (négociation obsolète).

### 1.3 Stratégies recommandées

- **Gouvernance des données CRM** : corriger les 19 anomalies, ajouter une règle de validation empêchant la clôture d’un deal sans montant positif.
- **Scorecard fournisseur** : lier les paiements aux objectifs de ponctualité, avec actions prioritaires sur Hyderabad (réduction du retard à <20 jours) et Bordeaux (renégociation des contrats).
- **Alertes automatiques** : déclencher une revue direction pour toute négociation restée bloquée plus de 90 jours.

---

## Partie 2 – Analyse Excel / Power BI : livraisons, valeurs bloquées et risques par région

### 2.1 Sources de données

- **Feuil2** : tableau croisé des valeurs bloquées par client.
- **ERP_Deliveries_VL01N** : 389 lignes de livraison avec statut, retard, site d’expédition, montant bloqué.
- **ERP_SalesOrders_SD** : 800 commandes (produits, montants, conditions de paiement, offsets).
- **Graphiques Power BI (Africa, Europe, Middle East)** : risques financiers, CA gagné et win rates par région.

### 2.2 Constats clés

#### Clients avec valeurs bloquées (Feuil2)

| Client                | Valeur bloquée (Md€) |
|-----------------------|----------------------|
| Reliance Industries   | 7,02                 |
| Indian Air Force      | 4,56                 |
| Indonesian Air Force  | 4,05                 |
| **Total**             | **15,63**            |

#### Livraisons retardées (extraits de VL01N)

- Retard de 365 jours : livraison VL0080000029 (Indonesian Air Force, Rafale F3‑R) – 783,5 M€ bloqués.
- Retard de 60 jours : livraison VL0080000003 (Reliance, Rafale F4) – 882 M€ bloqués.
- Retard de 90 jours : livraison VL0080000005 (Air France, Falcon 10X) – 301,6 M€ bloqués.

*De très nombreux retards de 60, 90, 180 et 365 jours sont répertoriés. Le site de Nagpur (Inde) concentre les retards les plus longs.*

#### Risques financiers par région (Power BI)

| Région       | CA gagné (Md€) | Taux de victoire | Risque financier lié aux retards |
|--------------|----------------|------------------|----------------------------------|
| Europe       | 30             | 70,7 %           |                                  |
| Moyen‑Orient | 11             | 74,7 %           | 41,29 Md€ (toutes régions)       |
| Asie         | 7              | n.d.             |                                  |
| Afrique      | n.d.           | 86,7 %           |                                  |

**Le risque financier total atteint 41,29 Md€**, soit plus que le CA gagné cumulé.  
L’Europe est le marché le plus important mais aussi celui où la concurrence est la plus rude (taux de perte ~30 %).

### 2.3 Recommandations issues de l’analyse Excel / Power BI

- **Déblocage progressif** : pour les trois clients prioritaires (Reliance, Indian AF, Indonesian AF), proposer des livraisons partielles avec paiements échelonnés afin de réduire l’encours bloqué.
- **Offensive logistique en Europe** : communiquer sur les plans de réduction des délais pour reconquérir les appels d’offres perdus (Belgique, Finlande, etc.).
- **Priorité Inde** : utiliser les obligations offset pour créer des co‑entreprises locales de logistique et accélérer les livraisons sur les sites indiens (Nagpur).
- **Tableau de bord unique** : croiser chaque semaine les valeurs bloquées, les retards par site et les échéances contractuelles, avec revue mensuelle des dix plus gros blocages.

---

## Stratégies transversales et plan d’action global

1. **Réduction des retards extrêmes**  
   Audit des causes des retards >180 jours (sous‑traitance, douanes, offsets) avec une task force dédiée.

2. **Harmonisation des données**  
   Aligner la définition du « retard » entre MIGO, production et CRM. Nettoyer les 19 anomalies CRM.

3. **Couverture financière**  
   Provisionner 5 % du risque total (~2 Md€) pour absorber les pénalités et négocier des déblocages anticipés.

| Priorité | Action | Délai | Impact attendu |
|----------|--------|-------|----------------|
| 1 | Débloquer les 15,6 Md€ de valeurs bloquées (Reliance, Indian AF, Indonesian AF) | 90 jours | Réduction du risque de 15 Md€, amélioration de trésorerie |
| 2 | Éliminer tous les retards >180 jours | 6 mois | Zéro retard critique |
| 3 | Corriger les anomalies CRM et déployer le scorecard fournisseur | 3‑6 mois | Fiabilité du pilotage |

---

## Conclusion

Dassault Aviation dispose d’un pipeline commercial exceptionnel (307 Md€) et d’un chiffre d’affaires gagné significatif (48 Md€). Cependant, les retards logistiques et fournisseurs génèrent un risque financier de 41,3 Md€ et des valeurs bloquées de 15,6 Md€ sur trois clients seulement. L’Inde est à la fois le marché le plus prometteur et le plus exposé. La mise en œuvre des actions ci-dessus – gouvernance des données, réduction des retards critiques, déblocage progressif des paiements – permettra de réduire le risque, d’améliorer la trésorerie et de renforcer la compétitivité de Dassault sur ses marchés clés.

---

*Ce README accompagne les analyses réalisées en Python (pandas, numpy) et en Excel/Power BI (tableaux croisés, graphiques régionaux). Les fichiers sources sont disponibles dans le dépôt.*
---

## Partie 2 - Audit Commercial & Architecture RevOps (Salesforce CRM)

### 2. Audit Commercial & Architecture RevOps (Salesforce CRM)
Afin de piloter le pipeline commercial de bout en bout (de l'acquisition marketing jusqu'à la signature), j'ai construit un environnement Salesforce complet en respectant les meilleures pratiques d'architecture relationnelle.

**Phase 1 : Acquisition Marketing & Gestion des Leads**
*   **Import & Mapping :** Injection de plus de 1200 Leads via le Data Import Wizard. Nettoyage et mapping manuel des champs pour forcer l'alignement du tracking (`Lead Source`, `Lead Status`) et corriger les erreurs d'affectation natives de l'assistant.
*   **Analyse du ROI Marketing :** Création d'un rapport de performance d'acquisition (Stacked Bar Chart) croisant la source des Leads avec leur statut d'avancement. 
*   **Insight :** Le rapport a permis de démontrer que si le salon "Aero India" génère un volume de prospection massif, c'est le "Dubai Airshow" qui offre le meilleur taux de conversion qualitatif (*Working / Converted*), justifiant une réallocation du budget événementiel.

**Phase 2 : Architecture de Données & Import Relationnel**
*   **Création de clés primaires (`External_ID`) :** Configuration de l'Object Manager pour créer des champs de type "ID Externe" sur les objets `Account`, `Contact` et `Opportunity`.
*   **Migration Relationnelle :** Import séquentiel pour recréer la hiérarchie métier. Les Contacts ont été dynamiquement rattachés aux Comptes (Accounts) via la clé `External_ID`.
*   **Utilisation de Dataloader.io :** Recours à l'outil ETL professionnel pour injecter 1 000 Opportunités et lier les Activités commerciales (`Tasks`) à la fois aux Humains (`WhoId`) et aux Contrats (`WhatId`).

**Phase 3 : Reporting Avancé & Dashboarding**
*   **Le "S&OP - Executive Control Tower" :** Un Lightning Dashboard regroupant le pipeline en cours (Funnel Chart), la volumétrie d'activités par Sales Rep (Stacked Bar), et les KPI financiers.
*   **Le Filtre Croisé (Neglected Deals) :** Création d'un rapport ciblant les *Opportunities WITHOUT Activities*. J'ai pu identifier une faille managériale critique : **19 contrats majeurs en cours de négociation sont totalement à l'abandon**, sans aucun appel ou meeting récent. 
*   **Impact :** Ce rapport a permis d'isoler **6,13 Milliards d'euros** de chiffre d'affaires potentiel laissé en suspens par les équipes de ventes. *(Voir capture `assets/salesforce_neglected_deals.png`)*.


### 📌 Avant-propos

L’audit du pipeline commercial Salesforce a révélé des dysfonctionnements critiques dans le pilotage de l’activité, du lead jusqu’à la signature. Malgré un volume important de données (915 leads, 19 opportunités actives, plus de 1 200 leads injectés), plusieurs anomalies mettent en péril la fiabilité du forecast et l’efficacité opérationnelle.

### Constats chiffrés

| Indicateur | Valeur |
|------------|--------|
| Total leads injectés | 1 200+ |
| Leads par source principale | Aero India : 119, Dubai Airshow : 111, LinkedIn : 123, etc. |
| Opportunités en cours (Q2-2026) | 19 |
| **Montant total du pipeline potentiel abandonné** | **6,13 milliards d’euros** |
| Opportunités sans aucune activité (Task, appel, meeting) | 19/19 (100% des deals actifs sont en fait inactifs) |
| Disparité taux de conversion | Aero India : fort volume, faible conversion ; Dubai Airshow : meilleur taux de conversion |

### Lieux / sources concernés

- **Événements** : Aero India Bangalore, Dubai Airshow, DSEI London, Air Show Le Bourget, Euronaval
- **Canaux** : Embassy Referral, Partner Network, LinkedIn, Government RFP, Diplomatic Channel, External
- **Comptes impactés** : Rolls-Royce India, Lockheed Martin India, Indian Air Force, Indian Navy, Egyptian Air Force, Indonesian Air Force, Kuwait Air Force, Singapore Air Force, UAE Air Force, Reliance Defence, etc.

---

### 🔍 Insights clés (par ordre d’importance décroissante)

### 1. **Pipeline dormant critique – 6,13 Mds€ sans aucune activité commerciale**
- **Détail** : Les 19 opportunités ouvertes (stades : Negotiation, Proposal, Qualification, Prospecting) n’ont aucune `Task` associée (ni appel, ni email, ni réunion) depuis leur création.
- **Exemple** : *Aviation GE – Indonesia (7 units)* à 275 M€ ; *Royalty FTA – Egypt (26 units)* à 2,3 Mds€ – tous sans suivi.
- **Risque** : Abandon pur et simple du deal, perte de chiffre d’affaires, détérioration de la relation client.

### 2. **Le ROI marketing masqué : volume ≠ performance**
- **Détail** : Aero India génère un lead massif (119), mais le taux de conversion qualitatif (*Working / Converted*) est inférieur à celui de Dubai Airshow (111 leads mais taux d’avancement bien supérieur).
- **Insight stratégique** : Continuer à investir dans Aero India sans correction de la qualification serait une perte budgétaire.
- **Opportunité** : Réallocation des dépenses événementielles vers Dubai Airshow + LinkedIn.

### 3. **Absence de tracking post-lead – faille managériale**
- **Détail** : Aucune règle ni workflow n’oblige les commerciaux à créer une activité (`Task`) après la création d’une opportunité. Le rapport « Opportunities without Activities » a dû être créé manuellement.
- **Conséquence** : La direction n’a aucun moyen de détecter les deals stagnants avant qu’ils ne soient perdus. La revue commerciale mensuelle est fondée sur des données obsolètes.

### 4. **Probabilités incohérentes avec l’âge des opportunités**
- **Détail** : Plusieurs opportunités affichent 75% de probabilité alors qu’elles ont été créées 1 jour avant la clôture prévue (date irréaliste).
- **Exemple** : *Training Program – Germany (2 units)* – créé le 09/05/2026, clôture prévue le 04/04/2026 (date passée), probabilité 75%.
- **Impact** : Forecast financier totalement biaisé ; trésorerie et S&OP non fiables.

### 5. **Canaux « External » et « Diplomatic Channel » sous-exploités en reporting**
- **Détail** : Ces sources représentent plusieurs opportunités (ex : *Spare Parts Package – Qatar* via Diplomatic Channel) mais ne sont jamais analysées en performance.
- **Potentiel** : Peut-être une source de conversion lente mais à très haute valeur. À investiguer.

---

###  Recommandations immédiates

1. **Forcer la création d’une activité sous 48h** pour toute opportunité en stage *Negotiation/Proposal*.
2. **Réactiver les 19 deals** en affectant un responsable commercial senior.
3. **Recalibrer les probabilités** avec une matrice stage + âge.
4. **Automatiser un dashboard hebdomadaire** « Neglected Deals » envoyé au VP Sales.
5. **Réallouer le budget événementiel** après analyse du ROI par Lead Source.

---



## 🛠️ Comment reproduire ce projet
1. Cloner ce repository.
2. Exécuter les scripts Python situés dans `/scripts` (`generate_crm.py` et `generate_erp.py`) pour regénérer les datasets simulés.
3. Consulter les notebooks Jupyter dans `/notebooks` pour voir la démarche d'Analyse Exploratoire (EDA) et de détection d'anomalies.
4. Consulter les dashboards et modélisations S&OP dans `/dashboards`.
5. Retrouver les captures des analyses dans le dossier `/assets`.
