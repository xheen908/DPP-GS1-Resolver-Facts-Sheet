# DPP GS1 Resolver

<div align="center">

**Enterprise-Ready GS1 Digital Link Resolver with Native ESPR & JTC 24 Compliance**  
*Enterprise-Ready GS1 Digital Link Resolver mit nativer ESPR & JTC 24 Compliance*

[🇬🇧 English Version](#-english) &nbsp;•&nbsp; [🇩🇪 Deutsche Version](#-deutsch)

</div>

---

<a name="-english"></a>
# 🇬🇧 English

**A pragmatic, enterprise-ready GS1 Digital Link Resolver with native ESPR & CEN/CENELEC JTC 24 compliance.**

This repository provides the core infrastructure for issuing and resolving EU Digital Product Passports (DPP). Engineered specifically for the **Textiles, Apparel, and Footwear industry**—the primary first-wave sector under the EU Ecodesign for Sustainable Products Regulation (ESPR)—it is designed to make regulatory compliance reliable, auditable, and effortless to integrate into existing brand and manufacturing workflows.

---

## 🛡️ Executive Summary: Compliance Made Simple and Dependable

> *"A well-engineered Digital Product Passport shouldn't require rebuilding your IT landscape or keeping awake at night worrying about audit compliance. It should simply do the right thing—every single time."*

Under the EU Ecodesign for Sustainable Products Regulation (ESPR) and the EU Strategy for Sustainable and Circular Textiles, garments and textile products placed on the EU market require verifiable digital passports detailing material provenance, environmental footprints, and circularity data. In practice, the primary operational challenge for fashion brands, sourcing teams, and suppliers is keeping passport data consistent, accurate, and aligned with official standards without creating costly data silos.

This system solves that challenge through a clean, **stateless pass-through architecture**:

1. **Zero Redundant Data Storage (Zero-DB):** The resolver does not maintain a secondary product database. Data sovereignty stays where it belongs: inside your trusted master systems (ERP, PLM, PIM). The resolver queries current product records on demand, validates them against official schemas, cryptographically signs the payload, and serves it in real time.  
   **The benefit:** No desynchronized databases, no redundant storage liabilities, and zero extra GDPR overhead.
2. **Built-in Compliance Firewall:** The system incorporates a deterministic validation engine aligned with the CEN/CENELEC JTC 24 standard. If required regulatory attributes (such as the `carbonFootprint`, fiber composition, or economic operator details) are missing, the resolver flags this immediately via **HTTP 422**.  
   **The benefit:** Complete peace of mind. Non-compliant or incomplete records cannot be inadvertently published to customs, market surveillance authorities, or customers.

---

## ⚖️ Engineering Philosophy: Deterministic Verification

Translating statutory guidelines and standards into software requires precision and defensive design. This resolver is developed around the principle of **Compliance-Driven Architecture**:

- **Strict Schema Enforcement:** Incoming payloads are never accepted on trust. Every attribute—from fiber breakdowns to toxicological safety checks—is validated against normative W3C schemas and regulatory criteria before any passport credential is generated.
- **Fail-Safe by Default:** The resolver adheres to fail-closed principles: if data cannot be verified or lacks mandatory fields, the issue is surfaced transparently at ingestion rather than during a customs check or audit.
- **Reproducible Verification:** The entire validation workflow is verified via automated end-to-end test suites running in isolated environments, ensuring consistent behavior across deployments.

---

## 🏛️ Architecture & Standards

### 1. GS1 Digital Link & Content Negotiation (Single Source of Truth)
Rather than cluttering care labels with multiple disjoint barcodes, the system uses **a single GS1 Digital Link** per garment. The resolver automatically detects the client and context, delivering the appropriate format in single-digit milliseconds:

* **Regulators & Market Surveillance (`Accept: application/ld+json`):** Receives the cryptographically signed W3C Verifiable Credential passport compliant with CEN/CENELEC JTC 24.
* **Recyclers, Sorters & Industry 4.0 (`Accept: application/asset-administration-shell+json`):** Delivers standardized *Asset Administration Shell* (AAS) payloads for automated fiber sorting, automated disassembly, and circular textile platforms.
* **PLM, ERP & Supply Chain Partners (`Accept: application/json`):** Serves clean, normalized data for easy software ingestion.
* **Consumers & Shoppers (`Accept: text/html`):** Renders transparent provenance, care and repair instructions, and circularity information directly in mobile browsers, or gracefully redirects to your brand experience.

### 2. Full White-Labeling
The resolver functions as an unobtrusive backend enabler. In the cryptographic signatures of issued product passports, the official issuer appears **exclusively as your brand** (e.g., `did:web:brand.example.com`), never a third-party vendor.

### 3. Cryptography & Hardware Security
- Ready for woven NFC smart tags and cryptographic QR labels (Proof of Authenticity).
- Native support for Enterprise Key Management Services (KMS / HSM).

---

## 🧪 Testing & Verification

The solution is backed by an automated end-to-end test suite that verifies the compliance engine under real-world conditions:

```text
✓ E2E: Delivers complete, fully valid JTC 24 / ESPR textile passport (HTTP 200)
✓ E2E: Blocks live request when mandatory batch number is missing (HTTP 422)
✓ E2E: Blocks live request when manufacturing date is missing (HTTP 422)
✓ E2E: Blocks live request when carbon footprint is missing (HTTP 422)
✓ E2E: Blocks live request when economic operator is missing (HTTP 422)
✓ E2E: (Content Negotiation): Delivers Asset Administration Shell (AAS) format
✓ E2E: (Content Negotiation): Delivers flat ERP JSON
```

---

## 🧵 Future-Ready: Adapting to Textile Delegated Acts & Circularity Rules

Under the ESPR textile roadmap, brands must accommodate continuously evolving requirements—from microplastic shedding benchmarks and recycled content thresholds to repairability indexes and take-back mandates.

**Why stateless matters for regulatory longevity:**  
Because the resolver holds no static product database, it remains resilient as regulations evolve. When new textile attributes (such as recycled cotton ratios or chemical compliance standards) are introduced, they can simply be surfaced in your PLM/ERP. The resolver dynamically reflects the updated attributes in the cryptographic passport without requiring database migrations or data schema overhauls.

**Modular Capabilities Ready for Production:**
* **W3C Cryptographic Proofs:** Automated signing of JSON-LD using URDNA2015 and enterprise hardware KMS.
* **CSW-CERTEX Connectivity:** Prepared for automated digital customs verification at EU borders.
* **Textile Ecodesign Extensions:** Pre-configured mappings for `dpp:careInstructions`, `dpp:fiberComposition`, `dpp:repairabilityScore`, and `dpp:takeBackInformation`.

---

## 🔌 Flexible Data Integration

Every enterprise has its own operational rhythm. The resolver supports multiple integration patterns to suit your existing setup:

1. **Real-Time API (Live Pull):** The resolver queries your internal ERP or PLM system on demand via secure REST or GraphQL endpoints.
2. **Event-Driven Push (Message Queues):** Your systems publish an event to a message queue (e.g., Kafka, RabbitMQ) upon batch finishing or final quality release.
3. **Scheduled Batch Ingestion:** For legacy or batch-oriented setups, automated daily batch feeds (CSV/XML) can be ingested into a secure staging buffer.

---

## 🔄 Data Flow: How Data Moves Through the Resolver

The resolver provides an authenticated **Data Ingestion API** for onboarding garment batches directly from your PLM or ERP.

### Step 1: Submit Garment Data (ERP/PLM → Resolver)

A standard `POST` request transfers the product record:

```http
POST /v1/ingest
Host: gs1.brand.example.com
Authorization: Bearer <API_KEY>
Content-Type: application/json

{
  "gtin": "04219589148911",
  "serial": "SER12345",
  "batch": "LOT-2026-TX4",
  "manufacturingDate": "2026-05-15T08:00:00Z",
  "manufacturingFacility": "Porto Technical Weaving & Assembly Hub",
  "carbonFootprint": { "value": 7.4, "unit": "KGM", "substance": "CO2eq" },
  "fiberComposition": [
    { "fiber": "Organic Cotton (GOTS certified)", "percentage": 70 },
    { "fiber": "Recycled Post-Consumer Polyamide", "percentage": 25 },
    { "fiber": "Elastane", "percentage": 5 }
  ],
  "economicOperator": {
    "name": "Heritage Textile Works AG",
    "vatID": "CHE-123.456.789",
    "address": "Industriestrasse 10, 8005 Zürich, CH"
  }
}
```

**Response (HTTP 201 Created):**
```json
{
  "gs1_link": "https://gs1.brand.example.com/01/04219589148911/21/SER12345",
  "qr_code_url": "https://gs1.brand.example.com/qr/04219589148911/SER12345",
  "status": "READY",
  "compliance_check": "PASSED"
}
```

The generated `gs1_link` is printed on the woven care label or encoded into an NFC tag. From this point on, it resolves correctly for any scanning client.

### Step 2: Automated Validation

If any mandatory field is missing, the resolver immediately returns clear diagnostics:

```json
{
  "status": "REJECTED",
  "compliance_check": "FAILED",
  "missing_fields": ["carbonFootprint", "economicOperator"],
  "error": "HTTP 422 – Unprocessable Entity: Mandatory ESPR fields missing."
}
```

### Step 3: Dynamic Resolution

Clients resolve the link automatically via Content Negotiation as detailed below.

---

## 🚀 Integration & API

The resolver is built to run smoothly as a managed, high-availability service, keeping pace with regulatory updates without administrative overhead for your engineering team.

**Interactive Endpoint Examples:**

The resolver supports standard HTTP `Accept` headers as well as simple query parameters for convenience.

### 1. Regulators & Auditors (W3C VC JSON-LD)
Fulfills EU ESPR & CEN/CENELEC JTC 24 specifications for textile products.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=vc
```

**Sample Payload:**
```json
{
    "@context": [
        "https://www.w3.org/ns/credentials/v2",
        "https://vocabulary.uncefact.org/untp/v1",
        {
            "schema": "http://schema.org/",
            "dpp": "https://ec.europa.eu/espr/dpp/v1#",
            "tex": "https://ec.europa.eu/espr/textiles/v1#",
            "idpp": "https://v-ledger.com/voc/"
        }
    ],
    "id": "urn:dpp:gtin:04219589148911:serial:SER12345:v1",
    "type": [
        "VerifiableCredential",
        "DigitalProductPassport"
    ],
    "issuer": "did:web:brand.example.com",
    "validFrom": "2026-06-03T23:17:09.673Z",
    "credentialSubject": {
        "id": "https://brand.example.com/dpp/01/04219589148911/21/SER12345",
        "type": "Product",
        "schema:name": "All-Weather Technical Merino Parka",
        "schema:description": "Weather-resistant technical outerwear crafted from certified organic merino wool and recycled performance fibers.",
        "dpp:modelIdentifier": "04219589148911",
        "dpp:instanceIdentifier": "SER12345",
        "dpp:batchNumber": "LOT-2026-TX4",
        "dpp:hsCode": "62014010",
        "dpp:originCountry": "PT",
        "dpp:manuals": [
            "https://brand.example.com/care/merino-parka-wash-guide.pdf",
            "https://brand.example.com/circularity/takeback-and-repair.pdf"
        ],
        "dpp:expectedLifeSpan": {
            "value": 10,
            "unit": "ANN"
        },
        "dpp:manufacturingDate": "2026-05-15T08:00:00Z",
        "dpp:manufacturingFacility": "Porto Technical Weaving & Finishing Facility",
        "dpp:substancesOfConcern": false,
        "dpp:carbonFootprint": {
            "value": 7.4,
            "unit": "KGM",
            "substance": "CO2eq"
        },
        "tex:materialComposition": [
            {
                "material": "Organic Merino Wool (RWS certified)",
                "percentage": 70
            },
            {
                "material": "Recycled Post-Consumer Polyamide (GRS certified)",
                "percentage": 25
            },
            {
                "material": "Elastane",
                "percentage": 5
            }
        ],
        "tex:careInstructions": [
            "WASH_30C_GENTLE",
            "DO_NOT_BLEACH",
            "LINE_DRY_IN_SHADE",
            "IRON_LOW"
        ],
        "tex:durabilityWashCycles": 50,
        "tex:repairabilityScore": 8.5,
        "dpp:supplyChainEvents": [
            {
                "type": "urn:epcglobal:cbv:bizstep:commissioning",
                "date": "2026-05-15T08:00:00Z",
                "location": "Porto Weaving Facility",
                "actor": "Heritage Textile Works AG"
            },
            {
                "type": "urn:epcglobal:cbv:bizstep:inspecting",
                "date": "2026-05-16T10:00:00Z",
                "location": "European Textile Testing Institute",
                "actor": "OEKO-TEX Inspection Services"
            },
            {
                "type": "urn:epcglobal:cbv:bizstep:shipping",
                "date": "2026-05-18T14:30:00Z",
                "location": "Rotterdam Central Logistics Hub",
                "actor": "EuroFreight Logistics"
            }
        ],
        "dpp:economicOperator": {
            "type": "Manufacturer",
            "schema:name": "Heritage Textile Works AG",
            "schema:vatID": "CHE-123.456.789",
            "schema:address": "Industriestrasse 10, 8005 Zürich, CH"
        },
        "idpp:hardware": {
            "chipType": "Woven NFC Smart Tag (Type 5)",
            "status": "AUTHENTIC",
            "counter": 1
        }
    },
    "proof": {
        "type": "DataIntegrityProof",
        "cryptosuite": "eddsa-rdfc-2022",
        "created": "2026-06-03T23:17:09.673Z",
        "verificationMethod": "did:web:brand.example.com#key-1",
        "proofPurpose": "assertionMethod",
        "proofValue": "zuLVd8fhTe3ybLfFmX8FwLRoPn2FzgwmmqmcfWW1aZ7UVv4dqFMndRDk37GuH4V1v5Ztq6H6nCpk18otB8Q18HCH"
    }
}
```

### 2. Consumer Web & Mobile View (`format=public`)
Structured JSON-LD object ready for customer-facing mobile web views and scan experiences.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=public
```

### 3. Circular Economy & Sorting Facilities (`format=aas`)
Asset Administration Shell encapsulation for automated textile sorting and recycling plants.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=aas
```

### 4. ERP & PLM Systems (`format=erp`)
Lightweight, normalized data object for straightforward internal inventory and catalog ingestion.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=erp
```

---

## 📅 Deployment Roadmap

| Phase | Scope | Typical Duration |
|---|---|---|
| **Phase 1** | Integration scoping: Data interfaces, PLM/ERP field mapping, authentication | Weeks 1–2 |
| **Phase 2** | Sandbox setup, E2E testing, and Compliance Firewall calibration | Weeks 3–6 |
| **Phase 3** | Staging to production cutover, documentation handover & monitoring | Weeks 7–8 |

**Estimated time to live: ~8 weeks** for a fully validated, production-ready GS1 Resolver.

---

## ⚠️ Note on Namespace Standards (Transparency)

The `dpp:` namespace prefix (`https://ec.europa.eu/espr/dpp/v1#`) utilized in this resolver represents the working consensus established across the CoE-DPP, TNO, and CIRPASS-2 reference architectures.

While the European Commission has adopted the ESPR regulation legislatively, publication of the final normative RDF/JSON-LD vocabulary remains in progress. Once the Commission finalizes the official URI schemas, updating this resolver is a **single configuration setting**, ensuring zero downtime or rework for production data.

---

<a name="-deutsch"></a>
# 🇩🇪 Deutsch

**Pragmatischer, Enterprise-Ready GS1 Digital Link Resolver mit nativer ESPR & JTC 24 Compliance.**

Dieses Repository enthält die Kern-Infrastruktur zur Ausstellung und Auflösung Digitaler Produktpässe (DPP). Entwickelt speziell für die **Textil-, Bekleidungs- und Schuhindustrie** – die zentrale Pilot- und Leitbranche unter der EU-Ökodesign-Verordnung (ESPR) – gewährleistet das System verlässliche, auditfeste und reibungslose Compliance nach aktuellen EU-Vorgaben.

---

## 🛡️ Executive Summary: Compliance einfach und zuverlässig umgesetzt

> *"Ein durchdachter Digitaler Produktpass sollte keine IT-Landschaften auf den Kopf stellen und keine schlaflosen Nächte bereiten. Er sollte einfach funktionieren – verlässlich und jederzeit prüfbar."*

Mit der EU-Ökodesign-Verordnung (ESPR) und der EU-Strategie für nachhaltige Textilien müssen Bekleidung und Textilwaren digital, transparent und verifizierbar dokumentiert werden (Faserzusammensetzung, CO₂-Fußabdruck, Zirkularität). Die größte praktische Herausforderung für Modemarken, Einkäufer und Lieferanten besteht darin, diese Daten entlang verzweigter Lieferketten bereitzustellen, ohne neue, fehleranfällige Datensilos aufzubauen.

Dieses System löst diese Aufgabe über eine **schlanke, zustandslose (Stateless) Architektur**:

1. **Keine redundante Datenhaltung (Zero-DB):** Das System speichert keine Produktdaten dauerhaft. Die Datenhoheit verbleibt vollständig in Ihren bestehenden Master-Systemen (ERP, PLM, PIM). Der Resolver fragt Stammdaten bei Bedarf in Echtzeit ab, validiert sie gegen offizielle Schemas, signiert sie kryptografisch und liefert sie direkt aus.  
   **Der Vorteil:** Keine desynchronisierten Datenbanken, kein doppelter Pflegeaufwand und keine zusätzlichen Datenschutzrisiken.
2. **Integrierte Compliance-Firewall:** Ein deterministisches Validierungsmodul prüft Anfragen nach dem CEN/CENELEC JTC 24 Standard. Fehlen gesetzlich erforderliche Attribute (wie CO₂-Fußabdruck, Faseranteile oder Angaben zum Wirtschafts-Akteur), meldet der Resolver dies sofort per **HTTP 422**.  
   **Der Vorteil:** Maximale Sicherheit im Betriebsalltag. Unvollständige oder fehlerhafte Produktpässe können gar nicht erst versehentlich an Zoll, Marktüberwachungsbehörden oder Endkunden ausgeliefert werden.

---

## ⚖️ Entwicklungsphilosophie: Deterministische Validierung

Die Übertragung regulatorischer Anforderungen in saubere Software erfordert methodische Präzision und defensive Architektur:

- **Strikte Schema-Prüfung:** Eingehende Daten werden niemals ungeprüft übernommen. Jedes Attribut – von der Faseranalyse bis zu Schadstoffprüfungen – wird vor der Pass-Erstellung gegen normative W3C-Schemas und gesetzliche Kriterien abgeglichen.
- **Sicherheit durch Voreinstellung (Fail-Closed):** Bei unvollständigen Daten stoppt der Prozess sofort an der Schnittstelle, anstatt erst bei Zollkontrollen oder Audits aufzufallen.
- **Reproduzierbare Qualitätssicherung:** Alle Validierungsschritte sind durch automatisierte End-to-End-Tests in isolierten Umgebungen abgesichert.

---

## 🏛️ Architektur & Standards

### 1. GS1 Digital Link & Content Negotiation (Single Source of Truth)
Statt Pflegeetiketten mit mehreren Barcodes zu überfrachten, genügt **ein einziger GS1 Digital Link** pro Kleidungsstück. Der Resolver erkennt den jeweiligen Kontext in Millisekunden und liefert das passende Format:

* **Behörden & Marktüberwachung (`Accept: application/ld+json`):** Erhalten den kryptografisch signierten W3C Verifiable Credential Pass nach JTC 24 Standard.
* **Recycling- & Sortieranlagen (`Accept: application/asset-administration-shell+json`):** Erhalten die standardisierte *Asset Administration Shell* (AAS) für automatisierte Fasersortierung, Demontage-Leitfäden und Kreislaufplattformen.
* **ERP- & PLM-Systeme (`Accept: application/json`):** Erhalten flache, saubere Stammdaten für den Software-Import.
* **Endkunden & Shopper (`Accept: text/html`):** Erhalten transparente Herkunfts-, Pflege- und Reparaturnachweise direkt im mobilen Browser oder werden auf Ihr Marken-Frontend weitergeleitet.

### 2. Echtes White-Labeling
Der Resolver fungiert als unaufdringlicher technologischer Enabler. Als offizieller Aussteller (Issuer) in den Signaturen erscheint **ausschließlich Ihre Marke** (z.B. `did:web:brand.example.com`), nicht der Software-Hersteller.

### 3. Kryptografie & Hardware-Sicherheit
- Kompatibel mit gewebten NFC-Smart-Tags und kryptografischen QR-Labels (Echtheitsnachweis).
- Direkte Anbindung an Enterprise Key Management Services (KMS / HSM).

---

## 🧪 Testing & Verifikation

Die Test-Suite prüft die Kernfunktionen unter praxisnahen Bedingungen:

```text
✓ E2E: Liefert vollständigen, absolut validen JTC 24 / ESPR Textil-Pass aus (HTTP 200)
✓ E2E: Blockiert Live-Request, wenn gesetzliche Batch-Number fehlt (HTTP 422)
✓ E2E: Blockiert Live-Request, wenn Herstellungsdatum fehlt (HTTP 422)
✓ E2E: Blockiert Live-Request, wenn CO2-Fußabdruck fehlt (HTTP 422)
✓ E2E: Blockiert Live-Request, wenn Wirtschafts-Akteur fehlt (HTTP 422)
✓ E2E: (Content Negotiation): Liefert Asset Administration Shell (AAS) Format
✓ E2E: (Content Negotiation): Liefert flaches ERP-JSON
```

---

## 🧵 Zukunftssicherheit für Textil-Ökodesign & Kreislaufwirtschaft

Im Rahmen der ESPR-Roadmap für Textilien müssen Marken dynamische Auflagen erfüllen – von Faser-Zertifizierungen und Recyclinganteilen bis hin zu Haltbarkeits- und Rücknahmevorgaben.

**Warum zustandslos zukunftssicher bedeutet:**  
Da der Resolver keine statische Produktdatenbank führt, bleibt er von regulatorischen Ergänzungen unberührt. Werden neue Pflichtfelder für bestimmte Textilkategorien verabschiedet, müssen diese lediglich im bestehenden ERP/PLM hinterlegt werden. Der Resolver bindet die Attribute in Echtzeit in den signierten Produktpass ein – ohne Datenbankmigrationen oder Systemumbauten.

**Erweiterungsmodule für den Produktiveinsatz:**
* **W3C Cryptographic Proofs:** Automatisiertes Signieren via URDNA2015 und Hardware-KMS.
* **CSW-CERTEX Anbindung:** Vorbereitet für das EU Customs Single Window zur automatisierten Zollabfertigung.
* **Erweiterte Ökodesign-Attribute:** Vorkonfigurierte Felder wie `dpp:careInstructions`, `dpp:fiberComposition`, `dpp:repairabilityScore` und `dpp:takeBackInformation`.

---

## 🔌 Flexible Datenanbindung

Der Resolver passt sich Ihren IT-Gegebenheiten flexibel an:

1. **Echtzeit-Abfrage (Live API):** Der Resolver fragt Daten bei Bedarf direkt über REST- oder GraphQL-Endpunkte aus Ihrem ERP/PLM ab.
2. **Ereignisgesteuerter Push (Message Queues):** Ihr System meldet fertige Chargen an eine Message Queue (z.B. Kafka, RabbitMQ).
3. **Periodischer Batch-Import:** Für Batch-orientierte Systeme können täglich Exporte in einen sicheren Zwischenspeicher geladen werden.

---

## 🔄 Datenfluss: Wie Produktdaten übergeben werden

Über eine gesicherte **Data Ingestion API** nimmt der Resolver Daten direkt aus Ihrem PLM oder ERP entgegen.

### Schritt 1: Produktdaten übergeben (ERP/PLM → Resolver)

Ein strukturierter `POST`-Aufruf übergibt den Datensatz:

```http
POST /v1/ingest
Host: gs1.brand.example.com
Authorization: Bearer <API_KEY>
Content-Type: application/json

{
  "gtin": "04219589148911",
  "serial": "SER12345",
  "batch": "LOT-2026-TX4",
  "manufacturingDate": "2026-05-15T08:00:00Z",
  "manufacturingFacility": "Porto Technical Weaving & Assembly Hub",
  "carbonFootprint": { "value": 7.4, "unit": "KGM", "substance": "CO2eq" },
  "fiberComposition": [
    { "fiber": "Organic Cotton (GOTS certified)", "percentage": 70 },
    { "fiber": "Recycled Post-Consumer Polyamide", "percentage": 25 },
    { "fiber": "Elastane", "percentage": 5 }
  ],
  "economicOperator": {
    "name": "Heritage Textile Works AG",
    "vatID": "CHE-123.456.789",
    "address": "Industriestrasse 10, 8005 Zürich, CH"
  }
}
```

**Antwort (HTTP 201 Created):**
```json
{
  "gs1_link": "https://gs1.brand.example.com/01/04219589148911/21/SER12345",
  "qr_code_url": "https://gs1.brand.example.com/qr/04219589148911/SER12345",
  "status": "READY",
  "compliance_check": "PASSED"
}
```

Der `gs1_link` wird auf das Pflegeetikett gedruckt oder im NFC-Tag hinterlegt und ist ab diesem Moment für alle Zielgruppen auflösbar.

### Schritt 2: Automatische Validierung

Fehlen gesetzliche Pflichtangaben, gibt die API eine eindeutige Rückmeldung:

```json
{
  "status": "REJECTED",
  "compliance_check": "FAILED",
  "missing_fields": ["carbonFootprint", "economicOperator"],
  "error": "HTTP 422 – Unprocessable Entity: Mandatory ESPR fields missing."
}
```

### Schritt 3: Auflösung je nach Anfragesteller

Die Bereitstellung erfolgt dynamisch via Content Negotiation wie nachfolgend beschrieben.

---

## 🚀 Integration & API

Der Resolver lässt sich als hochverfügbarer Managed Service betreiben, wodurch regulatorische Aktualisierungen ohne internen Wartungsaufwand einfließen.

**Beispielhafte Endpunkte:**

### 1. Behörden & Auditoren (W3C VC JSON-LD)
Erfüllt die Vorgaben von EU ESPR und CEN/CENELEC JTC 24 für Textilerzeugnisse.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=vc
```

### 2. Endkunden & Webansicht (`format=public`)
Strukturiertes JSON-LD für Web-Ansichten und Scans auf Mobilgeräten.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=public
```

### 3. Kreislaufwirtschaft & Sortieranlagen (`format=aas`)
Asset Administration Shell Format für Sortieranlagen und Recyclingbetriebe.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=aas
```

### 4. ERP- & PLM-Systeme (`format=erp`)
Schlankes Datenobjekt für den direkten Software-Import.
```text
https://gs1-resolver-demo.v-ledger.com/v1/resolve/01/04219589148911/21/SER12345?format=erp
```

---

## 📅 Einführungs-Fahrplan

| Phase | Inhalt | Richtwert |
|---|---|---|
| **Phase 1** | Schnittstellendefinition: Datenanbindung, PLM/ERP-Feldmapping, Authentifizierung | Woche 1–2 |
| **Phase 2** | Sandbox-Setup, E2E-Tests und Firewall-Kalibrierung | Woche 3–6 |
| **Phase 3** | Produktivübernahme, Dokumentationsübergabe und Monitoring-Einrichtung | Woche 7–8 |

**Typische Gesamtdauer: ca. 8 Wochen** bis zum vollständig validierten Produktivbetrieb.

---

## ⚠️ Hinweis zu Namensraum-Standards (Transparenz)

Das im Resolver genutzte `dpp:`-Prefix (`https://ec.europa.eu/espr/dpp/v1#`) entspricht der abgestimmten Konvention führender Referenzarchitekturen (CoE-DPP, TNO, CIRPASS-2).

Sobald die Europäische Kommission die offizielle Vokabular-URI final publiziert, erfolgt die Anpassung im Resolver über **eine einzige Konfigurationszeile** – ohne Ausfallzeiten oder Änderungen an Produktivdaten.
