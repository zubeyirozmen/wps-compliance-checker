WPS Compliance Checker
> AI-assisted Welding Procedure Specification (WPS) validation tool for EN ISO 15614-1, built by a metallurgical engineer to address a real workflow pain in steel fabrication QA.
![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-red.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
[![Standard](https://img.shields.io/badge/Standard-EN%20ISO%2015614--1-green.svg)]()
---
Why This Project Exists
During a 650-ton structural steel welding project for ThyssenKrupp, I experienced firsthand how much engineering time is spent manually cross-checking WPS documents against EN ISO 15614-1 — flipping between PDF standards, Excel sheets, and qualification records to verify a single parameter.
Most existing welding software (WeldAssistant, WeldNote, WeldTrace) handles WPS creation and storage well, but none of them apply AI to interpret compliance failures and suggest corrective actions. This project explores that gap: a hybrid system where deterministic rules guarantee correctness on safety-critical numbers, and AI provides plain-language engineering explanations.
It is built as a portfolio project, not a commercial product. Final WPS qualification still requires a notified body.
---
What It Does
Upload a WPS document (PDF, Excel, or JSON) and the tool will:
Extract the welding parameters (parent material, thickness, process, heat input, preheat, etc.)
Validate them against EN ISO 15614-1 essential variables and cross-references (EN ISO 4063, EN ISO 6947, EN 1011-2, EN ISO 15609-1)
Generate a professional compliance report (PDF) with corrective actions
Explain every PASS / WARNING / FAIL in plain engineering language using Claude AI
---
Architecture: Hybrid Rules + AI
The core design principle: the AI never decides PASS or FAIL.
```
┌─────────────────────────────────────────────────────────┐
│ Input: PDF / Excel / JSON                               │
└────────────────────┬────────────────────────────────────┘
                     ↓
┌────────────────────────┬────────────────────────────────┐
│ Rule Engine (Python)   │ AI Reasoning (Claude)          │
│ ─ Material groups      │ ─ Natural language explanation │
│ ─ Thickness ranges     │ ─ Corrective action suggestion │
│ ─ Heat input formula   │ ─ Field extraction (fallback)  │
│ ─ Preheat minimums     │                                │
│ DETERMINISTIC          │ CONSTRAINED BY RULES           │
└────────────────────┬───┴───────────────┬────────────────┘
                     ↓                   ↓
              ┌──────────────────────────────┐
              │ Cross-validation             │
              │ AI claims checked against    │
              │ rule engine before output    │
              └──────────────┬───────────────┘
                             ↓
              ┌──────────────────────────────┐
              │ Output: Compliance PDF       │
              │ + JSON technical report      │
              └──────────────────────────────┘
```
Why this matters: In safety-critical engineering, you cannot let an LLM hallucinate a thickness limit or a heat input range. The rule engine encodes the standard's tables as Python lookups — these are the authoritative numbers. The AI only interprets why a check failed and how to fix it. This separation enables zero-hallucination on safety numbers while still giving the user a human-readable summary.
---
Features
Multi-format Input
PDF — extracts text and tables via `pdfplumber`
Excel (.xlsx) — reads cell-by-cell with label-value detection
JSON — direct structured input
Interactive form — fill the parameters manually
Rule Engine Coverage
Material groups per CEN ISO/TR 15608 (subset: steel groups 1.1–1.4, austenitic 8.1–8.2)
Welding processes per EN ISO 4063
Welding positions per EN ISO 6947 (with position coverage matrix per Table 5)
Thickness qualification range per EN ISO 15614-1 Table 4
Heat input tolerance ±25% per §10.4.2 (with process efficiency factors)
Preheat minimums per EN 1011-2 (simplified)
Required WPS fields per EN ISO 15609-1
AI Layer
Field extraction fallback — if structural reading misses critical fields, Claude extracts them from raw text, instructed never to invent values
Compliance interpretation — receives the validated JSON report and writes an executive summary plus corrective actions
Constrained prompting — AI cannot contradict rule engine verdicts or introduce new numerical thresholds
Output
Professional PDF compliance report (signature blocks, standard references, color-coded verdicts)
JSON technical report (for integration with other systems)
---
Screenshots
Web UI — Validation View
Upload area with sample files, mode selector
![Web UI](docs/screenshots/01_ui.png)
Validation Report — PASS Case
Color-coded check table, AI summary, downloadable PDF
![Pass Case](docs/screenshots/02_pass.png)
Validation Report — FAIL Case
Outside qualified thickness range, heat input violation, missing fields
![Fail Case](docs/screenshots/03_fail.png)
Generated PDF Report
Professional output suitable for QA documentation
![PDF Report](docs/screenshots/04_pdf_report.png)
---
Quickstart
```bash
# Clone
git clone https://github.com/zubeyirozmen/wps-compliance-checker.git
cd wps-compliance-checker

# Install
pip install -r requirements.txt

# (Optional) Set Anthropic API key for AI explanations
# Without this, the tool falls back to a templated summary
export ANTHROPIC_API_KEY="sk-ant-..."

# Run the web UI
streamlit run ui/streamlit_app.py
```
Open `http://localhost:8501` in your browser.
Run Tests
```bash
python tests/test_validator.py
# Expected: 8 passed, 0 failed
```
---
Project Structure
```
wps-compliance-checker/
├── rules/
│   └── en_iso_15614_1.py     # Standard tables — single source of truth
├── core/
│   ├── validator.py           # Rule engine (deterministic)
│   ├── ai_explainer.py        # Claude integration (interpretation only)
│   ├── pdf_generator.py       # Professional PDF report
│   └── file_reader.py         # PDF / Excel / JSON parsers
├── ui/
│   └── streamlit_app.py       # Web interface
├── sample_wps/                # Example WPS files (good + bad cases)
├── tests/
│   └── test_validator.py      # Unit tests
├── docs/
│   └── screenshots/           # Documentation images
├── requirements.txt
├── LICENSE
└── README.md
```
---
Limitations & Scope
This is an engineering pre-check assistant, not a certification authority:
Covers a representative subset of EN ISO 15614-1 essential variables, not every clause
Standard tables are simplified for clarity (real standard has more edge cases — CTOD, special operating conditions, multi-process welds)
Final WPS qualification still requires a witnessed PQR per EN ISO 15614-1 and approval by an EN ISO 3834-certified body
Built for steel fabrication (groups 1.x and 8.x); other material groups not yet covered
Tested with text-based PDFs — scanned/image PDFs would need OCR (not implemented)
---
Market Context
For interested reviewers — I researched the existing welding software landscape before building this:
Tool	Standards	AI Layer	Approach
WeldAssistant	ISO + ASME + AWS	None	Database + form templates
WeldNote	ISO + ASME + AWS	None	Cloud + WPS/PQR matrix
WeldTrace	ASME + AWS + ISO 15614	None	Workflow + welder qualification
WeldEye	ISO + ASME + AWS	None	Enterprise modular suite
WPSAmerica	AWS + ASME	None	10,000+ pre-qualified WPSs
This project	EN ISO 15614-1	Claude	Rule engine + AI interpretation
The commercial tools are mature but form-based. None of them apply LLM reasoning to compliance explanation. That's the gap this project explores.
---
What This Project Demonstrates
For hiring engineers reviewing this code:
Domain expertise — accurate encoding of EN ISO 15614-1 essential variables, cross-referenced with EN ISO 4063, 6947, 15609-1, and EN 1011-2
System design — separation of deterministic logic from probabilistic AI, ensuring safety-critical numbers cannot hallucinate
Engineering pragmatism — modular, tested, with explicit scope boundaries
AI-augmented productivity — Claude is used where it adds value (interpretation, fallback extraction) and excluded where it would harm reliability
Real-world input handling — PDF, Excel, JSON parsers acknowledge that real WPS documents are not structured data
---
About the Author
I am a Metallurgical and Materials Engineer with hands-on experience in structural steel fabrication, welding quality control, and project execution under international standards (ASME, AWS, CSA). I led a 650-ton structural steel welding project for ThyssenKrupp and have worked across supplier quality, CAD/CAM design, and metallurgical operations.
Currently based in Germany with EU work authorization, fluent in English, improving German (A2 → B1). Open to Welding Engineer, Quality Engineer, and Project Engineer roles in steel fabrication and heavy industry across Germany, the Netherlands, and Belgium.
LinkedIn: linkedin.com/in/zübeyir-özmen
Email: ozmen.zubeyir@outlook.com
---
License
MIT License — see LICENSE file.
Welding standard names (EN ISO, ASME, AWS) belong to their respective organizations. This project does not reproduce protected standard text; it implements engineering interpretations of publicly known parameters.
---
Built with Python, Streamlit, ReportLab, and Anthropic Claude. 2026.
