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


# 📋 RAPPORT STRATÉGIQUE : AUDIT PROCURE-TO-PAY (P2P)

**Objet :** Analyse des anomalies de facturation et plan de sécurisation des flux P2P.
**Impact Financier Total Identifié :** 48,96 Millions d'Euros (M€)

---

## 1. Synthèse Exécutive

L'audit approfondi du cycle Procure-to-Pay met en lumière des défaillances systémiques dans les contrôles internes. Les mécanismes de validation des factures (3-Way Match) ne filtrent pas efficacement les anomalies, entraînant une perte de trésorerie directe de **48,96 Millions d'Euros** sur la période analysée.

Le problème est critique car il se concentre presque exclusivement sur des **partenaires stratégiques de rang 1 (Tier 1)** de l'industrie aéronautique et défense (Safran, Pratt & Whitney, Thales).

## 2. Analyse des Vulnérabilités & Impact Financier

L'hémorragie financière se divise en deux failles opérationnelles distinctes :

### A. Échec du Contrôle Physique (Ghost Invoices) : 6,93 M€
Le principe du "3-Way Match" (correspondance stricte entre Commande, Réception et Facture) est structurellement contourné. Des paiements sont émis pour des équipements non réceptionnés physiquement.

*   **Cas Critique - Safran Aircraft Engines :** La ligne de commande (EBELN) 409 indique 10 unités commandées et reçues, mais **11 unités payées**. Le coût de cette unité "fantôme" s'élève à **5,55 M€** (correspondant au tarif d'un équipement majeur, type moteur d'aéronef). Le paiement d'un actif de cette valeur sans vérification d'entrée en stock (ex: MIGO dans SAP) révèle un forçage manuel (Bypass) lors de la validation comptable.
*   **Volume global :** 11 factures fantômes détectées pour un total de 6,93 M€.

### B. Dérive des Prix Négociés (Cash Leakage) : 42,03 M€
Outre les écarts de quantité, un volume massif d'écarts de prix (Price Variance) est constaté. 51 factures ont été honorées pour des montants supérieurs aux conditions négociées par le service Achats.

*   **Top 3 des fournisseurs par volume de surfacturation :**
    1.  **Pratt & Whitney** : 9,94 M€
    2.  **Elbit Systems India** : 8,24 M€
    3.  **Safran Aircraft Engines** : 6,07 M€

## 3. Insights Stratégiques

L'analyse des données soulève trois constats opérationnels majeurs :

1.  **L'effet "Too Big to Fail" :** La concentration des anomalies sur les fournisseurs ultra-critiques (Safran, P&W) suggère que la validation des factures s'effectue sous pression de la production (risque de rupture de la chaîne d'assemblage). Ces fournisseurs bénéficient d'un rapport de force favorable, facilitant l'absorption de leurs erreurs de facturation par notre comptabilité.
2.  **Défaillance de la configuration ERP :** Les seuils de tolérance (Tolerance Limits) dans le système d'information sont inopérants. Un ERP correctement paramétré doit bloquer automatiquement (statut "Block for Payment") toute facture présentant un écart unitaire de 5,5 M€.
3.  **Érosion de la performance Achats :** Les gains obtenus lors des négociations contractuelles en amont sont annulés lors de la phase de paiement en aval. Le processus "Order-to-Cash" des fournisseurs s'avère techniquement plus robuste que notre processus de contrôle "Procure-to-Pay".

## 4. Plan d'Action Recommandé (Roadmap ROI)

Afin de recouvrer la trésorerie et d'endiguer le risque, le plan d'action de remédiation suivant est proposé :

### Phase 1 : Cash Recovery Immédiat (Sous 15 jours)
*   **Campagne de recouvrement :** Lancement des procédures de réclamation auprès des fournisseurs du Top 5 pour exiger l'émission d'Avoirs (Credit Memos) couvrant les 48,96 M€.
*   **Mesure conservatoire :** Suspension temporaire des prochaines échéances de paiement vers Pratt & Whitney et Safran dans l'attente d'une régularisation comptable et de l'émission des avoirs.

### Phase 2 : Verrouillage Systémique (Sous 3 mois)
*   **Audit et "Hard Stop" ERP :** Reconfiguration stricte des clés de tolérance dans l'ERP (ex: SAP transaction MIRO). Instauration d'un blocage technique infranchissable si l'écart dépasse un pourcentage minime (ex: 0.5%) ou un montant fixe absolu (ex: 1 000€).
*   **Workflow d'escalade :** Tout déblocage de facture hors tolérance nécessitera obligatoirement une double validation (Acheteur responsable de la commodité + Contrôleur de Gestion). Fin des validations unilatérales par la comptabilité.

### Phase 3 : Business Review Fournisseurs (Moyen Terme)
*   **KPI "Invoice Accuracy Rate" :** Intégration du taux d'exactitude de facturation dans les revues de performance fournisseurs (Quarterly Business Reviews) et utilisation de cette métrique comme malus lors des futures négociations contractuelles.

## Partie 5 : Revue Stratégique Globale (Analyse croisée P2P, CRM et O2C)

### 5.1 Rétrospective Méthodologique
L'approche d'audit a été menée de manière itérative afin d'obtenir une vision transverse de la chaîne de valeur :

1.  **Phase 1 - Audit Financier Procure-to-Pay (P2P) :** Extraction et analyse des données de facturation (ERP), ayant mis en évidence 48,96 M€ d'anomalies (échecs du *3-Way Match* et surfacturations).
2.  **Phase 2 - Audit Opérationnel Fournisseurs (CRM) :** Croisement des données financières avec les données opérationnelles (Cases CRM) pour évaluer la corrélation entre les défaillances de facturation et les litiges qualité.
3.  **Phase 3 - Audit Financier Order-to-Cash (O2C) :** Élargissement du périmètre à la santé financière de l'entreprise via l'analyse des créances clients (Power BI) pour évaluer la pression globale sur le Besoin en Fonds de Roulement (BFR).

### 5.2 Analyse Croisée des Données

**A. Performance Opérationnelle Fournisseurs (P2P vs CRM)**
L'analyse croisée met en évidence une corrélation structurelle : le Top 3 des fournisseurs présentant les écarts financiers les plus élevés (*Pratt & Whitney, Elbit Systems, Safran*) correspond également aux acteurs comptabilisant la majorité des litiges qualité dans le CRM. 

*Note méthodologique sur le volume d'incidents :* Le volume brut affiché de 30 571 incidents (Cases) est la résultante d'un **produit cartésien** généré lors de la jointure des tables de données (incidents multipliés par le nombre de lignes de commandes/articles associés). Bien que le volume absolu soit artificiellement dilaté par la structure du modèle, la distribution proportionnelle des incidents confirme une défaillance opérationnelle marquée chez ces fournisseurs de rang 1.

**B. Exposition au Risque Client (O2C)**
Le tableau de bord Power BI indique une exposition significative au risque de liquidité sur le cycle de vente :
*   Sur un chiffre d'affaires global de 117 Md, la métrique **"Cash at Risk" s'établit à 24 Md**.
*   Ce montant correspond aux **20,63%** du CA catégorisés en statut "Overdue" (échu non payé).
*   L'analyse de la typologie des débiteurs montre une forte concentration de ces créances douteuses auprès de comptes souverains étatiques (ex: Croatian Air Force, UAE Air Force).

### 5.3 Insights Stratégiques

Cette vue à 360° permet de dégager trois axes de réflexion majeurs pour l'organisation :

1.  **Tension asymétrique sur le BFR (Effet Ciseaux) :** L'organisation est exposée à un déséquilibre de trésorerie. En amont (P2P), les décaissements sont gonflés par des anomalies de facturation fournisseurs. En aval (O2C), les encaissements sont ralentis par un volume massif de créances échues (24 Md).
2.  **Corrélation Qualité/Conformité :** Les fournisseurs dits "Tier 1", historiquement considérés comme sécurisés, concentrent simultanément les risques opérationnels (litiges CRM) et financiers (surfacturation). 
3.  **Gestion des données en Silo :** L'identification de ces risques a nécessité l'agrégation manuelle de bases de données distinctes. L'absence d'interface native entre l'ERP (facturation/commandes) et le CRM (incidents) empêche un contrôle en temps réel du cycle d'achat.

### 5.4 Plan d'Action Recommandé (Roadmap)

**Action 1 : Rationalisation des flux de trésorerie (Court terme)**
*   **P2P :** Conditionner le paiement des factures fournisseurs (notamment P&W et Safran) à l'apurement des litiges qualité en cours et à l'émission des avoirs financiers exigés.
*   **O2C :** Lancer une task force de recouvrement ciblée sur les créances souveraines (24 Md), impliquant la direction commerciale et les affaires publiques pour gérer la dimension diplomatique de ces comptes.

**Action 2 : Sécurisation du Système d'Information (Moyen terme)**
*   Instaurer des règles de blocage stricts (*Hard Stops*) dans l'ERP empêchant techniquement le forçage des validations de factures si le *3-Way Match* échoue.

**Action 3 : Refonte contractuelle (Long terme)**
*   Intégrer les métriques de fiabilité croisées (précision de facturation + taux d'incidents CRM épurés) comme critères de pénalités contractuelles (SLA) lors des prochains renouvellements avec les équipementiers Tier 1.

---



## 🛠️ Comment reproduire ce projet
1. Cloner ce repository.
2. Exécuter les scripts Python situés dans `/scripts` (`generate_crm.py` et `generate_erp.py`) pour regénérer les datasets simulés.
3. Consulter les notebooks Jupyter dans `/notebooks` pour voir la démarche d'Analyse Exploratoire (EDA) et de détection d'anomalies.
4. Consulter les dashboards et modélisations S&OP dans `/dashboards`.
5. Retrouver les captures des analyses dans le dossier `/assets`.
