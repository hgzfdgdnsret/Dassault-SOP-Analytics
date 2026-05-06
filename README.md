 Dassault Aviation - Sales & Operations Planning (S&OP) Analytics 🛩️
## 📌 Executive Summary
Ce projet simule l'architecture Data d'un département S&OP chez Dassault Aviation. L'objectif est d'identifier et de résoudre les goulots d'étranglement capacitaires (Supply Chain) face à une explosion historique du carnet de commandes à l'export, avec un focus spécifique sur le marché indien ("Make in India").
Le projet croise des données commerciales (Salesforce CRM) et des données de production/supply chain (SAP ERP) pour fournir une vue exécutive à 360°.
## 🎯 Business Problem
La stratégie du "Make in India" impose de s'appuyer sur des fournisseurs locaux (Tata, HAL, Reliance). 
**La problématique :** Quels contrats (CRM) sont actuellement à risque à cause de retards spécifiques de nos fournisseurs indiens (ERP) ?
## ⚙️ Data Architecture & Tech Stack
L'architecture repose sur l'extraction, la transformation (ETL) et la visualisation de flux de données croisés.
- **CRM (Salesforce) :** Gestion du pipeline (Accounts, Contacts, Leads, Opportunities).
- **ERP (SAP MM/SD) :** Gestion logistique (Material Master, Purchase Orders, Goods Receipts MIGO, Vendor Invoices MIRO, Sales Orders).
- **ETL & Data Cleaning :** Python (Pandas) via Google Colab.
- **Data Visualization :** Power BI (Modélisation en étoile, DAX).
## 📊 Key Insights & S&OP Findings
*(Note : Tu rempliras cette partie avec tes conclusions une fois le dashboard fini)*
- 🔴 **Supply Chain Risk :** Les retards de livraison sur l'usine de Nagpur impactent X% des livraisons finales.
- 🟢 **Sales Performance :** Le Win Rate en Asie est de X%.
## 🛠️ Comment reproduire ce projet
1. Cloner ce repository.
2. Exécuter les scripts dans `/scripts` pour générer les données (Seeders).
3. Ouvrir les notebooks dans `/notebooks` pour visualiser les étapes de data cleaning.
4. Ouvrir le fichier `.pbix` dans `/dashboards` pour explorer les données sur Power BI.
