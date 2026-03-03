<p align="center">
  <img src="docs/images/facturation-interface.png" alt="Invoice creation interface" width="1200">
</p>

> 🇬🇧 English | [🇫🇷 Français](./README_FR.md)

![Factur-X](https://img.shields.io/badge/Factur--X-compliant-0a5645)
![License](https://img.shields.io/badge/License-LICENSE.md-lightgreen.svg)
![Documentation](https://img.shields.io/badge/Focus-Documentation-0095b1?style=flat)
![Bilingual](https://img.shields.io/badge/Lang-FR%20%2F%20EN-0a5645?style=flat)

<p align="center">
  <a href="https://palks-studio.com">
    <img src="https://img.shields.io/badge/Palks%20Studio-Website-0095b1?style=for-the-badge" />
  </a>
</p>


# Direct Invoice Engine (PHP + Dompdf + Factur-X)

Lightweight and deterministic invoice generation engine:  

- Receives data from an HTML form (POST)  
- Generates PDF via Dompdf  
- Generates Factur-X XML  
- Injects XML into the PDF (Factur-X compliant PDF)  
- Structured archiving per client and period  
- Generates traceability metadata file (`.meta.json`)  
- Optional invoice email delivery

The project emphasizes:  

- simplicity  
- autonomy (no critical external dependencies)  
- deterministic outputs  
- full traceability  
- readable architecture

---

## Features

- Automatic client creation (identifier-based deduplication)  
- Sequential yearly invoice numbering  
- Multi-line support  
- Multi-VAT rate handling  
- Factur-X generation (XML + PDF injection)  
- Pre-generation of paid invoices  
- Traceability metadata (SHA-256 hash)  
- Optional email delivery  
- Technical logging  
- Manual payment validation with traceability

### Payment validation

A secured interface allows:  

- viewing pending invoices  
- marking an invoice as paid  
- moving the paid invoice  
- updating metadata  
- feeding the revenue journal

Access is protected by authentication.

---

### Client lookup

An internal endpoint allows searching for an existing client  
using a business identifier (SIREN, SIRET, VAT or email).

Objectives:  

- prevent duplicate client creation  
- prefill interface data  
- speed up invoice entry

Access to this endpoint is protected by session.

---

## Project structure

```
automation/
│
├── engine/
│   ├── build_facturx_xml.php             → Factur-X XML generation
│   ├── inject_facturx.py                 → Injects Factur-X XML into PDF
│   ├── go.php                            → Batch automation engine for client invoicing
│   ├── alerts.php                        → Execution alerts and notifications
│   ├── mailer.php                        → Batch email delivery
│   └── templates/                        → Client invoice templates
│
├── tmp/                                  → Temporary engine files (non persistent)
├── counters/                             → Sequential counters (invoice numbering)
├── logs/                                 → Application technical logs
├── vendor/                               → PHP dependencies
├── clients/                              → End-client configuration
├── data/
│   ├── invoices/                         → Issued invoices
│   ├── acquittee/                        → Paid invoices
│   └── tmp_facturx/                      → Temporary Factur-X files
│ 
│── app.py                                → Python entry point (Factur-X injection)
├── passenger_wsgi.py                     → WSGI bridge for Phusion Passenger
│── purge_logs.php                        → Automatic application log cleanup
│
├── LICENCE.md                            → Conditions d’utilisation et cadre légal (FR)
├── LICENSE.md                            → Terms of use and legal Framework (EN)
│ 
├── docs/
│   ├── GUIDE_EXPLOITATION.md             → Guide d’exploitation du moteur Automation Batch (FR)
│   ├── OPERATIONS_GUIDE.md               → Batch Automation operations guide (EN)
│   ├── SECURITY_FR.md                    → Modèle de sécurité (FR)
│   ├── SECURITY.md                       → Security model and hardening guidelines (EN)
│   ├── NOTE_INTERNE_AUTOMATION_BATCH.md  → Note interne - Cadrage, Gouvernance et Structuration du Projet Automation Batch (FR)
│   ├── INTERNAL_NOTE_AUTOMATION_BATCH.md → Internal note — project structuring (EN)
│   ├── README_FR.md                      → Documentation générale du système (FR)
│   └── README.md                         → General system documentation (EN)
│
└── web/
    ├── client-lookup.php                 → Secure client lookup endpoint
    ├── facture-directe.php               → Secure invoice creation interface
    ├── go.php                            → Secure invoice generation endpoint
    └── mark_paid.php                     → Secure payment validation interface
```


---

## Processing flow

1. The form sends data via POST to `go.php`  
2. Session verification  
3. Minimal data validation  
4. Client resolution or creation  
5. Invoice number assignment  
6. Factur-X XML generation  
7. PDF generation via Dompdf  
8. Factur-X injection via Python script  
9. `.meta.json` traceability file creation  
10. (Optional) email delivery  
11. PDF download

---

## Requirements

- PHP 8+  
- `mbstring` extension  
- `iconv` extension  
- Dompdf  
- Python 3 (for Factur-X injection)  
- Write access to data directories

---

## Installation (summary)

1. Install PHP dependencies (Dompdf)  
2. Place the project outside the public web root when possible  
3. Verify write permissions on:
   - `data/`  
   - `clients/`  
   - `logs/`  
   - `counters/`  
4. Configure the Factur-X injection script  
5. Connect the HTML form to the `go.php` endpoint

---

## Security

The engine includes several protections:  

- session-based access control  
- server-side validation of critical fields  
- file-lock protected numbering  
- atomic file writes  
- technical logging  
- reliable Factur-X success detection  
- hardened PHP sessions (HttpOnly, Secure, SameSite)  
- basic login rate limiting  
- defensive HTTP headers  
- secrets stored outside the repository

### Production recommendations

- place the application outside the web root  
- protect sensitive endpoints  
- restrict filesystem permissions  
- monitor logs

---

## Traceability metadata

Each invoice generates:  

```
ALT-XXXX.meta.json
```


Containing:  

- invoice number  
- client_id  
- generation date  
- Factur-X status  
- totals (HT/VAT/TTC)  
- PDF SHA-256 hash

Purpose: auditability and integrity.

---

## Status

Project used in production in an automated invoicing context.

---

© Palks Studio — see LICENSE.md  
- https://palks-studio.com