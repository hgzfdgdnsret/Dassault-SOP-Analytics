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
## 📊 Déroulement de l'Analyse & Insights
### Partie 1.1 : Analyse Exploratoire (EDA) & Audit sous Python
Dans un premier temps, j'ai utilisé Python (Google Colab) pour auditer la qualité et le volume des données brutes. 
**Côté Ventes (Audit du Pipeline CRM) :**
- Un pipeline global extrêmement chargé : 63,08 Mds€ de deals signés (*Closed Won*), 57,72 Mds€ en négociation finale et 75,37 Mds€ au stade de proposition.
- **Détection d'anomalies :** Un script de Data Quality a permis d'isoler **19 deals** présentant des incohérences CRM majeures nécessitant une action managériale immédiate (ex: deals marqués gagnés mais facturés à 0€, ou négociations périmées).
**Côté Supply Chain (Audit des retards ERP/MIGO) :**
- La logistique est en forte tension : 428 réceptions sur 760 (soit plus de 56%) sont en retard.
- L'usine d'Hyderabad a été identifiée comme le goulot d'étranglement critique avec un délai moyen dépassant les 30 jours de retard, devant Bordeaux (~25 jours).
### Partie 1.2 : Modélisation du Risque S&OP (Power Query & Excel)
Pour répondre à la problématique, j'ai croisé les données de livraisons (ERP) avec les contrats clients (CRM) en utilisant Power Query. L'objectif était de lier un retard physique à une pénalité financière potentielle.
**Résultats clés :**
- 🔴 **Risque Financier Majeur :** Plus de 15,6 Milliards d'euros de chiffre d'affaires sont actuellement bloqués en raison de retards logistiques supérieurs à 30 jours.
- ⚠️ **Impact Client :** Le partenaire industriel *Reliance Industries* (Falcon) est le plus impacté avec 7 Mds€ en attente, suivi de *l'Indian Air Force* (Rafale) avec 4,5 Mds€. 
- **Conclusion stratégique :** Ces retards mettent en péril nos obligations liées au "Make in India". Le croisement de ces données permet désormais à la direction des opérations de prioriser l'allocation des pièces sur les lignes d'assemblage en fonction du risque financier de chaque client.
---
## 🛠️ Comment reproduire ce projet
1. Cloner ce repository.
2. Exécuter les scripts Python situés dans `/scripts` (`generate_crm.py` et `generate_erp.py`) pour regénérer les datasets simulés.
3. Consulter les notebooks Jupyter dans `/notebooks` pour voir la démarche d'Analyse Exploratoire (EDA) et de détection d'anomalies.
4. Consulter les dashboards et modélisations S&OP dans `/dashboards`.
5. Retrouver les captures des analyses dans le dossier `/assets`.
