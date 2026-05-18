# WPS Compliance Checker

> AI-assisted Welding Procedure Specification (WPS) validation concept tool, demonstrating a hybrid rule-engine + LLM architecture for engineering compliance workflows.

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-red.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Status](https://img.shields.io/badge/Status-Portfolio%20Demo-orange.svg)

---

## ⚠️ Disclaimer

This is an **educational portfolio project** demonstrating software architecture for engineering compliance tools. It is **not** a certified compliance product.

- This project does **not** reproduce, distribute, or substitute for any published international standard.
- Parameter references are educational interpretations of publicly known engineering principles.
- Standard names (EN ISO, ASME, AWS, CEN ISO/TR) belong to their respective organizations.
- For real WPS/PQR qualification, users must obtain licensed copies of the relevant standards and work with a notified body (e.g., EN ISO 3834-certified bodies).
- This tool does **not** constitute engineering certification or legal compliance verification.

---

## Why This Project Exists

While supporting structural steel fabrication projects, I observed how much engineering time is spent manually cross-checking welding procedure documents against international standards — toggling between PDF references, Excel sheets, and qualification records to verify a single parameter.

Most existing welding software handles document creation and storage well, but few apply modern AI to interpret compliance findings and suggest corrective actions in plain engineering language. This project explores that gap as a concept: a hybrid system where deterministic rules guarantee correctness on safety-critical numbers, and AI provides human-readable interpretation.

It is built as a portfolio piece to demonstrate engineering thinking and AI integration patterns, not as a commercial product.

---

## What It Does

Given a Welding Procedure Specification document (PDF, Excel, or JSON), the tool will:

1. **Extract** welding parameters (parent material, thickness, process, heat input, preheat, etc.)
2. **Validate** them against an educational rule set covering structural steel welding essentials
3. **Generate** a styled PDF report with corrective action notes
4. **Explain** every PASS / WARNING / FAIL in plain engineering language using Claude AI

---

## Architecture: Hybrid Rules + AI

The core design principle: **the AI never decides PASS or FAIL.**

```
┌─────────────────────────────────────────────────────────┐
│ Input: PDF / Excel / JSON                               │
└────────────────────┬────────────────────────────────────┘
                     ↓
┌────────────────────────┬────────────────────────────────┐
│ Rule Engine (Python)   │ AI Reasoning (Claude)          │
│ — Material groups      │ — Natural language explanation │
│ — Thickness ranges     │ — Corrective action suggestion │
│ — Heat input formula   │ — Field extraction (fallback)  │
│ — Preheat minimums     │                                │
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
              │        + JSON technical      │
              └──────────────────────────────┘
```

**Why this matters:** In safety-critical engineering, you cannot let an LLM hallucinate a thickness limit or a heat input range. The rule engine encodes engineering parameters as Python lookups — these are the authoritative numbers within the demo's scope. The AI only interprets *why* a check failed and *how* to fix it. This separation enables zero-hallucination on safety numbers while still giving the user a human-readable summary.

---

## Features

### Multi-format Input

- **PDF** — extracts text and tables via `pdfplumber`
- **Excel (.xlsx)** — reads cell-by-cell with label-value detection
- **JSON** — direct structured input
- **Interactive form** — fill the parameters manually

### Rule Engine Coverage (Demo Scope)

- Material group examples for common structural steels
- Welding process and position code lookups
- Thickness qualification range checks
- Heat input tolerance checks (with process efficiency factors)
- Preheat minimum checks for common steel groups
- Required-field presence checks for typical WPS documents

### AI Layer

- **Field extraction fallback** — if structural reading misses critical fields, Claude extracts them from raw text, instructed never to invent values
- **Compliance interpretation** — receives the validated JSON report and writes an executive summary plus corrective action notes
- **Constrained prompting** — AI cannot contradict rule engine verdicts or introduce new numerical thresholds

### Output

- Styled PDF report (signature blocks, color-coded verdicts) for demonstration purposes
- JSON technical report (for integration with other systems)

---

## Quickstart

```bash
# Clone
git clone https://github.com/zubeyirozmen/wps-compliance-checker.git
cd wps-compliance-checker

# Install
pip install -r requirements.txt

# (Optional) Set Anthropic API key for AI explanations
export ANTHROPIC_API_KEY="sk-ant-..."

# Run the web UI
streamlit run ui/streamlit_app.py
```

Open `http://localhost:8501` in your browser.

### Run Tests

```bash
python tests/test_validator.py
# Expected: 8 passed, 0 failed
```

---

## Project Structure

```
wps-compliance-checker/
├── rules/                     # Educational parameter tables
├── core/
│   ├── validator.py           # Rule engine (deterministic)
│   ├── ai_explainer.py        # Claude integration (interpretation only)
│   ├── pdf_generator.py       # PDF report generation
│   └── file_reader.py         # PDF / Excel / JSON parsers
├── ui/
│   └── streamlit_app.py       # Web interface
├── sample_wps/                # Example WPS files (good + bad cases)
├── tests/
│   └── test_validator.py      # Unit tests
├── requirements.txt
└── README.md
```

---

## Limitations & Scope

This is a **portfolio demonstration**, not a certification authority:

- Covers a representative subset of common essential variables, not every clause of any standard
- Simplified for clarity; real-world welding qualification has many edge cases (CTOD requirements, special operating conditions, multi-process welds, etc.)
- Final WPS qualification requires a witnessed PQR and approval by an EN ISO 3834-certified body
- Demo scope covers common structural steel groups; other material categories not yet implemented
- Tested with text-based PDFs — scanned/image PDFs would need OCR (not implemented)

---

## What This Project Demonstrates

For hiring engineers reviewing this code:

1. **Engineering thinking** — modeling real workflow problems in software, with appropriate scope boundaries
2. **System design** — separation of deterministic logic from probabilistic AI, ensuring safety-critical numbers cannot hallucinate
3. **Engineering pragmatism** — modular, tested, with explicit scope and limitations
4. **AI-augmented productivity** — Claude is used where it adds value (interpretation, fallback extraction) and excluded where it would harm reliability
5. **Real-world input handling** — PDF, Excel, JSON parsers acknowledge that real industry documents are not structured data

---

## About the Author

Metallurgical and Materials Engineer with hands-on experience in structural steel fabrication, welding quality control, and project execution. Background includes coordinating multi-hundred-ton structural steel projects, supplier quality audits (ISO 9001, 8D, FMEA), CAD/CAM design, and metallurgical operations.

Currently based in Germany with EU work authorization, fluent in English, improving German. Open to Welding Engineer, Quality Engineer, and Project Engineer roles in steel fabrication and heavy industry across Germany, the Netherlands, and Belgium.

- **Email:** ozmen.zubeyir@outlook.com

---

## License

MIT License — see [LICENSE](LICENSE) file.

This project does not reproduce protected standard text. All references to international standards are nominal and educational. The source code is released under MIT for educational and portfolio purposes.

---

*Built with Python, Streamlit, ReportLab, and Anthropic Claude. 2026.*
