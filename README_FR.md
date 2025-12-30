<p align="center">
  <img src="docs/images/Palks_Studio.png" alt="Palks Studio" width="600">
</p>

> 🇫🇷 Français | [🇬🇧 English](./README.md)

# Système d’automatisation – Facturation & Recettes

Ce dépôt contient un système d’automatisation de facturation conçu pour fonctionner :  

- sans CMS  
- sans SaaS externe  
- sans base de données  
- sans interface web exposée

L’ensemble repose sur des scripts PHP exécutés via cron, avec une architecture volontairement simple, lisible et auditable.

Le système permet :  

- la génération automatique de factures PDF (FR / EN)  
- l’envoi automatique des factures par email  
- le suivi des recettes par client (JSON)  
- l’export des recettes au format CSV (comptable)  
- une numérotation annuelle fiable des factures.

---

## Principes clés

- Un client = un fichier de configuration  
- Aucune donnée sensible exposée sur le web  
- Aucune dépendance à un service tiers de facturation  
- Traçabilité complète (logs, factures, recettes)  
- Exécution exclusivement en ligne de commande (CLI)

Ce système est conçu pour être :  

- robuste  
- prévisible  
- maintenable dans le temps  
- compréhensible sans connaissance avancée

---

## Structure du projet

```
automation/
├── engine/
│   ├── run.php                 → Moteur principal d’automatisation (cron / CLI) (FR)
│   │                           → Main automation engine (cron / CLI) (EN)
│   │
│   ├── vendor/                 → Dépendances PHP (ex: DomPDF) (FR)
│   │                           → PHP dependencies (e.g. DomPDF) (EN)
│   │
│   ├── mailer.php              → Envoi des emails avec facture en pièce jointe (FR)
│   │                           → Email sender with invoice attachment (EN)
│   │
│   └── templates/
│       └── invoice.html.php    → Template PDF de facture (bilingue FR / EN) (FR)
│                               → Invoice PDF template (bilingual FR / EN) (EN)
│
├── clients/
│   └── client_xxx.php          → Fiche client (seul fichier à modifier par client) (FR)
│                               → Client configuration file (only file to edit per client) (EN)
│
├── data/
│   ├── logs/
│   │   └── xxx.log             → Logs d’exécution par client (FR)
│   │                           → Execution logs per client (EN)
│   │
│   ├── revenues/
│   │   └── xxx.json            → Recettes cumulées (source comptable interne) (FR)
│   │                           → Cumulative revenues (internal accounting source) (EN)
│   │
│   ├── invoices/
│   │   └── xxx-2025-001.pdf    → Factures générées et archivées par client (FR)
│   │                           → Generated and archived invoices per client (EN)
│   │
│   └── counters/
│       └── xxx.json            → Compteur annuel de factures par client (FR)
│                               → Annual invoice counter per client (EN)
│
├── tools/
│   └── export_revenues_csv.php → Export des recettes au format CSV (comptabilité) (FR)
│                               → Revenue export to CSV format (accounting) (EN)
│
├── LICENSE.md                  → Terms of use and legal Framework
│
└── README.md                   → Documentation générale du système (FR)
                                → General system documentation (EN)
```


---

## Fonctionnement global

### 1. Configuration client

Chaque client est défini dans un fichier dédié :

```bash
clients/client_xxx.php
```


- l’identité du client  
- les informations de facturation  
- la devise  
- la langue (FR / EN)  
- le mode (`test` ou `live`)

C’est le seul fichier à modifier pour ajouter ou ajuster un client.

---

### 2. Exécution automatique (cron)

Le script principal est :

```
engine/run.php
```


Il est exécuté via une tâche cron (exemple quotidien ou mensuel).

À chaque exécution :  

- les clients actifs sont parcourus  
- une facture est générée si applicable  
- la facture est archivée  
- les recettes sont mises à jour  
- un email est envoyé au client  
- les logs sont écrits

---

### 3. Facturation PDF

- Les factures sont générées au format PDF via DomPDF  
- Le template est bilingue FR / EN  
- La langue dépend uniquement de la configuration du client  
- Les mentions légales (TVA, exonération, etc.) sont gérées automatiquement

Les factures sont archivées par client dans :

```bash
data/invoices/{client_id}/
```


---

### 4. Recettes (source comptable)

Pour chaque client, un fichier JSON est maintenu :

```bash
data/revenues/{client_id}.json
```


Il contient :  

- le total cumulé  
- l’historique détaillé des factures émises  
- la devise  
- les dates et numéros de facture

Ce fichier est la source comptable interne du système.

---

### 5. Export comptable (CSV)

Un outil d’export est fourni :

```bash
tools/export_revenues_csv.php
```


Il permet de générer des fichiers CSV exploitables par :  

- un tableur  
- un expert-comptable  
- un logiciel de comptabilité

Les CSV peuvent être supprimés et régénérés à tout moment
(sans impact sur les données sources).

---

## Sécurité

- Le moteur refuse toute exécution hors CLI  
- Aucun endpoint web n’est exposé  
- Les données sont stockées localement sur le serveur  
- Aucun accès direct n’est prévu depuis un navigateur

---

## Nettoyage et maintenance

- Les dossiers `logs/` et les exports CSV peuvent être nettoyés sans risque  
- Les dossiers `invoices/`, `revenues/` et `counters/` ne doivent jamais être supprimés  
- La numérotation des factures est automatique et annuelle

---

## État du projet

Statut : Stable – prêt pour une utilisation en production.

Le système est utilisé en conditions réelles,  
sans dépendance externe critique  
et conçu pour fonctionner de manière autonome sur le long terme

---

© Palks Studio — voir LICENSE.md  
- https://palks-studio.com