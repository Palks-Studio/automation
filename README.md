<p align="center">
  <img src="docs/images/Palks_Studio.png" alt="Palks Studio" width="600">
</p>

> 🇬🇧 English | [🇫🇷 Français](./README_FR.md)

# Automation System – Invoicing & Revenue Tracking

This repository contains an **invoice and revenue automation system** designed to operate:  

- without a CMS  
- without external SaaS services  
- without a database  
- without any exposed web interface  

The entire system relies on PHP scripts executed via cron jobs, with a deliberately **simple, readable, and auditable architecture**.

The system provides:  

- automatic PDF invoice generation (FR / EN)  
- automatic invoice delivery by email  
- per-client revenue tracking (JSON)  
- revenue export to CSV (accounting-ready)  
- reliable yearly invoice numbering

---

## Core principles

- One client = one configuration file  
- No sensitive data exposed on the web  
- No dependency on third-party invoicing services  
- Full traceability (logs, invoices, revenues)  
- CLI-only execution (no browser access)

This system is designed to be:  

- robust  
- predictable  
- maintainable over time  
- understandable without advanced knowledge

---

## Project structure

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


---

## Global workflow

### 1. Client configuration

Each client is defined in a dedicated file:

```bash
clients/client_xxx.php
```


This file contains:  

- client identity  
- billing information  
- currency  
- language (FR / EN)  
- execution mode (`test` or `live`)

This is the **only file** to edit when adding or adjusting a client.

---

### 2. Automated execution (cron)

The main script is:

```
engine/run.php
```


It is executed via a cron job (daily, monthly, or scheduled as needed).

At each execution:  

- active clients are processed  
- an invoice is generated if applicable  
- the invoice is archived  
- revenues are updated  
- an email is sent to the client  
- execution logs are written

---

### 3. PDF invoicing

- Invoices are generated as PDF files using DomPDF  
- The template supports both French and English  
- The language depends solely on the client configuration  
- Legal mentions (VAT, exemptions, etc.) are handled automatically

Invoices are archived per client in:

```bash
data/invoices/{client_id}/
```


---

### 4. Revenues (accounting source)

For each client, a JSON file is maintained:

```bash
data/revenues/{client_id}.json
```


It contains:

- cumulative total  
- detailed invoice history  
- currency  
- invoice numbers and dates

This file is the **internal accounting source** of the system.

---

### 5. Accounting export (CSV)

An export tool is provided:

```bash
tools/export_revenues_csv.php
```


It generates CSV files usable by:  

- spreadsheets  
- accountants  
- accounting software

CSV files can be deleted and regenerated at any time  
(with no impact on source data).

---

## Security

- The engine refuses execution outside CLI  
- No web endpoint is exposed  
- Data is stored locally on the server  
- No direct browser access is possible

---

## Maintenance & cleanup

- `logs/` and CSV export files can be safely cleaned  
- `invoices/`, `revenues/`, and `counters/` must never be deleted  
- Invoice numbering is automatic and yearly

---

## Project status

Status: **Stable – production-ready**.

This system is used in real conditions,  
has no critical external dependencies,  
and is designed to operate autonomously over the long term.

---

© Palks Studio — see LICENSE.md  
https://palks-studio.com
